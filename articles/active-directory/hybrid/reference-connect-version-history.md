---
title: 'Azure AD Connect: Verziókiadási előzmények | Microsoft dokumentumok'
description: Ez a cikk az Azure AD Connect és az Azure AD Sync összes kiadását sorolja fel
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60dca30496aa17ad7add00ca4eca914cb58fddd7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655982"
---
# <a name="azure-ad-connect-version-release-history"></a>Az Azure AD Connect verziókiadásai
Az Azure Active Directory (Azure AD) csapata rendszeresen frissíti az Azure AD Connectet új funkciókkal és funkciókkal. Nem minden kiegészítés alkalmazható minden közönségre.

Ez a cikk segít nyomon követni a kiadott verziókat, és megérteni, hogy mik a változások a legújabb verzióban.

Ez a táblázat a kapcsolódó témakörök listáját tartalmazza:

Témakör |  Részletek
--------- | --------- |
Az Azure AD Connect szolgáltatásból való frissítés lépései | Különböző módszerek az előző verzióról a legújabb Azure AD Connect-kiadásra [való frissítéshez.](how-to-upgrade-previous-version.md)
Szükséges engedélyek | A frissítés alkalmazásához szükséges engedélyeket a fiókok és engedélyek című [témakörben](reference-connect-accounts-permissions.md#upgrade)tésszet.
Letöltés| [Töltse le az Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)szolgáltatást.

>[!NOTE]
>Az Azure AD Connect új verziójának kiadása olyan folyamat, amely több minőség-ellenőrzési lépést igényel a szolgáltatás működési funkcióinak biztosításához, és miközben ezt a folyamatot végighaladunk, az új kiadás verziószáma, valamint a kiadási állapot frissül, hogy tükrözze a legutóbbi állapotot.
A folyamat során a kiadás verziószáma egy "X" betűvel jelenik meg a kisebb kiadási számpozícióban, mint az "1.3.X.0" értékben - ez azt jelzi, hogy a dokumentumban szereplő kibocsátási megjegyzések minden "1.3" verzióra érvényesek. Amint véglegesítettük a kiadási folyamatot, a kiadási verziószám frissül a legutóbb kiadott verzióra, és a kiadási állapot "Letölthető és automatikus frissítésre" lesz frissítve.
Az Azure AD Connect nem minden kiadása lesz elérhető az automatikus frissítéshez. A kiadási állapot jelzi, hogy a kiadás elérhetővé válik-e automatikus frissítésre vagy csak letöltésre. Ha az automatikus frissítés engedélyezve volt az Azure AD Connect-kiszolgálón, akkor az a kiszolgáló automatikusan frissít az Automatikus frissítésre kiadott Azure AD Connect legújabb verziójára. Vegye figyelembe, hogy nem minden Azure AD Connect-konfiguráció jogosult az automatikus frissítésre. Kérjük, kövesse ezt a linket, hogy többet tudjon [meg az automatikus frissítésről](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> 2020. november 1-jétől kezdődően megkezdjük az eprecációs folyamat végrehajtását, amelynek során az Azure AD Connect több mint 18 hónappal ezelőtt kiadott verziói elavultak lesznek. Ekkor kezdjük ezt a folyamatot az Azure AD Connect összes kiadásának az 1.3.20.0-s verzióval (amely 2019.24.4-én jelent meg) és régebbi verziókkal, és minden alkalommal, amikor egy új verzió megjelenik, kiértékeljük az Azure AD Connect régebbi verzióinak epreprekációját.
>
> Az optimális támogatási élmény érdekében győződjön meg arról, hogy az Azure AD Connect legújabb verzióját futtatja. 
>
>Ha az Azure AD Connect elavult verzióját futtatja, előfordulhat, hogy nem rendelkezik a legújabb biztonsági javításokkal, teljesítményfejlesztésekkel, hibaelhárítási és diagnosztikai eszközökkel és szolgáltatásfejlesztésekkel, és ha támogatásra van szüksége, előfordulhat, hogy nem tudjuk biztosítani önnek a szervezet igényeinek megfelelő szintű szolgáltatást.
>
>Ha engedélyezte az Azure AD Connect szinkronizálásra hamarosan automatikusan megkezdi az állapotjelző értesítések fogadását, amelyek figyelmeztetik a közelgő eprekations-ra, amikor a régebbi verziók egyikét futtatja.
>
>Kérjük, olvassa el [ezt a cikket,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version) hogy többet megtudni, hogyan frissítheti az Azure AD Connect a legújabb verzióra.


## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Kiadás állapota
04/02/2020: Letölthető

### <a name="functional-changes-adsyncautoupgrade"></a>Funkcionális változások ADSyncAutoUpgrade 

- Az mS-DS-ConsistencyGuid szolgáltatás támogatása hozzáadva a csoportobjektumokhoz. Ez lehetővé teszi, hogy a csoportok áthelyezése az erdők között, vagy újra csoportokat az AD az Azure AD, ahol az AD-csoport objectID megváltozott, például ha egy AD-kiszolgáló újraépül egy csapás után. További információ: [Csoportok áthelyezése erdők között](how-to-connect-migrate-groups.md).
- Az mS-DS-ConsistencyGuid attribútum automatikusan be van állítva az összes szinkronizált csoporton, és semmit sem kell tennie a szolgáltatás engedélyezéséhez. 
- Eltávolította a Get-ADSyncRunProfile-t, mert már nincs használatban. 
- Módosította azt a figyelmeztetést, amely akkor jelenik meg, amikor az AD DS-összekötőfiók vállalati rendszergazdai vagy tartományi rendszergazdai fiókját próbálja használni, hogy több környezetet biztosítson. 
- Hozzáadott egy új parancsmacs, hogy távolítsa el az objektumokat az összekötő tér a régi CSDelete.exe eszköz törlődik, és ez helyébe az új Remove-ADSyncCSObject parancsmag. Az Remove-ADSyncCSObject parancsmag egy CsObject objektumot vesz fel bemenetként. Ez az objektum a Get-ADSyncCSObject parancsmag használatával olvasható be.

>[!NOTE]
>A régi CSDelete.exe eszközt eltávolították, és az új Remove-ADSyncCSObject parancsmagra cserélték 

### <a name="fixed-issues"></a>Hibák kijavítva:

- Javítottunk egy hibát a csoportvisszaíró erdő/szervezeti egység választójában az Azure AD Connect varázsló újrafuttatásakor, miután letiltja a szolgáltatást. 
- Bevezetett egy új hibalapot, amely akkor jelenik meg, ha a szükséges DCOM rendszerleíró értékek hiányoznak egy új súgóhivatkozással. Az információ a naplófájlokba is bekerül. 
- Kijavítottuk az Azure Active Directory szinkronizálási fiókjának létrehozásával kapcsolatos problémát, amely miatt a címtárbővítmények vagy a PHS engedélyezése sikertelen lehet, mert a fiók nem propagált az összes szolgáltatásreplikán a használat megkísérlése előtt. 
- Javítva egy hiba a szinkronizálási hibák tömörítési segédprogram, amely nem kezeli helyettesítő karakterek helyesen. 
- Javítva egy hiba az automatikus frissítésben, amely a kiszolgálót felfüggesztett állapotban hagyta. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Kiadás állapota
12/9/2019: Kiadás letölthető. Nem érhető el automatikus frissítéssel.
### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
- Frissítettük a Password Hash Sync for Azure AD tartományi szolgáltatások megfelelően figyelembe a Kitöltés a Kerberos-kiképek.  Ez teljesítményjavulást biztosít az AAD és az Azure AD tartományi szolgáltatások között történő jelszó-szinkronizálás során.
- A hitelesítési ügynök és a szolgáltatásbusz közötti megbízható munkamenetek támogatása.
- Ez a kiadás a TLS 1.2 protokollt kényszeríti a hitelesítési ügynök és a felhőszolgáltatások közötti kommunikációhoz.
- Dns-gyorsítótárat adtunk hozzá a hitelesítési ügynök és a felhőszolgáltatások közötti websocket kapcsolatokhoz.
- Hozzáadtuk a képességét, hogy adott ügynököt a felhőből az ügynökkapcsolat tesztelésére célozzon meg.

### <a name="fixed-issues"></a>Hibák kijavítva:
- Az 1.4.18.0-s kiadásban hiba történt, amelyben a DSSO PowerShell-parancsmagja a bejelentkezési windows hitelesítő adatait használta a ps futtatása közben megadott rendszergazdai hitelesítő adatok helyett. Ennek eredményeként az AADConnect felhasználói felületen nem lehetett engedélyezni a DSSO-t több erdőben. 
- Az AADConnect felhasználói felületen keresztül egyszerre engedélyezték a DSSO-t az összes erdőben

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Kiadás állapota
11/08/2019: Letölthető. Nem érhető el automatikus frissítéssel.

>[!IMPORTANT]
>Az Azure AD Connect jelen kiadásának belső sémája miatt, ha az AD FS megbízhatósági kapcsolat konfigurációs beállításait az MSOnline PowerShell használatával kezeli, akkor frissítenie kell az MSOnline PowerShell-modult az 1.1.183.57-es vagy újabb verzióra

### <a name="fixed-issues"></a>Hibák kijavítva:

Ez a verzió kijavítja a meglévő hibrid Azure AD-hez csatlakozó eszközökkel kapcsolatos problémát. Ez a kiadás egy új eszközszinkronizálási szabályt tartalmaz, amely kijavítja ezt a problémát.
Vegye figyelembe, hogy ez a szabályváltozás az elavult eszközök törlését okozhatja az Azure AD-ből. Ez nem ad okot aggodalomra, mivel ezeket az eszközobjektumokat az Azure AD nem használja a feltételes hozzáférés engedélyezése során. Egyes ügyfelek esetében a szabálymódosítással törölt eszközök száma meghaladhatja a törlési küszöbértéket. Ha az Azure AD-ben az eszközobjektumok törlése meghaladja az exportálási törlési küszöbértéket, azt javasoljuk, hogy engedélyezze a törléseket. [A törlési küszöbérték túllépése esetén a törpének a lehetővé tétel](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Kiadás állapota
9/28/2019: Automatikus frissítésre van kiadva a bérlők kiválasztásához. Nem tölthető le.

Ez a verzió egy olyan hibát javít, amely miatt egyes kiszolgálók at automatikusan frissítettek egy korábbi verzióról az 1.4.18.0-ra, és problémákat tapasztaltak az önkiszolgáló jelszó-visszaállítással (SSPR) és a jelszó-visszaírással kapcsolatban.

### <a name="fixed-issues"></a>Hibák kijavítva:

Bizonyos körülmények között az 1.4.18.0-s verzióra automatikusan frissített kiszolgálók nem engedélyezték újra az önkiszolgáló jelszó-visszaállítást és a jelszó-visszaírást a frissítés befejezése után. Ez az automatikus frissítési kiadás kijavítja ezt a problémát, és újra engedélyezi az önkiszolgáló jelszó-visszaállítást és a jelszóvisszaírást.

Kijavítottunk egy hibát a szinkronizálási hibák tömörítési segédprogramjában, amely nem kezelte megfelelően a helyettesítő karaktereket.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Egy olyan esetet vizsgálunk, amelyben egyes ügyfelek az Azure AD Connect ezen verziójára való frissítés után problémát tapasztalnak a meglévő hibrid Azure AD-s egyesített eszközökkel kapcsolatban. Azt tanácsoljuk az ügyfeleknek, akik üzembe helyezett hibrid Azure AD-csatlakozás elhalasztani frissítés erre a verzióra, amíg a kiváltó oka ezeknek a problémáknak teljes mértékben megértette és enyhíteni. A lehető leghamarabb további információkat nyújtunk.

>[!IMPORTANT]
>Az Azure AD Connect ezen verziójával egyes ügyfelek láthatják, hogy windowsos eszközeik egy része vagy egésze eltűnik az Azure AD-ből. Ez nem ad okot aggodalomra, mivel ezeket az eszközidentitásokat az Azure AD nem használja a feltételes hozzáférés engedélyezése során. További információ: [Az Azure AD Connect 1.4.xx.x eszköz eltűnése ismertetése](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Kiadás állapota
9/25/2019: Csak automatikus frissítésre alkalmas.

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
- Az új hibaelhárító eszközök segítenek a "felhasználó szinkronizálásának mellőzése", a "csoport nem szinkronizáló" vagy a "csoporttag nem szinkronizál" forgatókönyvekkel kapcsolatos hibaelhárításban.
- A nemzeti felhők támogatásának hozzáadása az AAD Connect hibaelhárítási parancsfájljában 
- Az ügyfeleket tájékoztatni kell arról, hogy a MIIS_Service elavult WMI-végpontjait eltávolították. A WMI-műveleteket most PS-parancsmagokon keresztül kell elvégezni.
- Biztonsági fejlesztés az AZUREADSSOACC objektum korlátozott delegálásának visszaállításával
- Szinkronizálási szabály hozzáadásakor/szerkesztésekor, ha a szabályban olyan attribútumok vannak, amelyek az összekötősémében vannak, de nem kerülnek hozzáadásra az összekötőhöz, az attribútumok automatikusan hozzáadva az összekötőhöz. Ugyanez igaz a szabály által hatással lévő objektumtípusra is. Ha valamit hozzáad az összekötőhöz, az összekötő a következő szinkronizálási ciklusban a teljes importáláshoz lesz megjelölve.
- Egy vállalati vagy tartományi rendszergazda az összekötő fiók már nem támogatott az új Azure AD Connect telepítések. A jelenlegi AAD Connect-telepítések egy vállalati vagy tartományi rendszergazda, mint az összekötő fiók nem érinti ezt a kiadást.
- A Szinkronizáláskezelőben a teljes szinkronizálás a szabály létrehozása/szerkesztése/törlése kor fut. Egy felugró ablak jelenik meg minden szabályváltozáson, amely értesíti a felhasználót, ha a teljes importálás vagy a teljes szinkronizálás futni fog.
- Hozzáadott kockázatcsökkentési lépéseket a jelszó hibák "összekötők > tulajdonságok > kapcsolat" oldal
- Hozzáadott egy eprecation figyelmeztetésa a szinkronizálási szolgáltatás kezelője az összekötő tulajdonságai lapon. Ez a figyelmeztetés értesíti a felhasználót, hogy az Azure AD Connect varázslón keresztül módosításokat kell végrehajtani.
- Új hiba a felhasználó jelszóházirendjével kapcsolatos problémák esetén.
- A csoportszűrés helytelen konfigurálásának megakadályozása tartomány- és szervezetiegység-szűrők szerint. A csoportszűrés hibát jelez, ha a beírt csoport tartománya/szervezeti egysége már ki van szűrve, és a felhasználó tartjuk a lépést a probléma megoldásáig.
- A felhasználók már nem hozhatnak létre összekötőt az Active Directory tartományi szolgáltatásokhoz vagy a Windows Azure Active Directoryhoz a Szinkronizálási szolgáltatáskezelő felhasználói felületén.
- A Szinkronizálási szolgáltatáskezelőben javítottuk az egyéni felhasználói felület-vezérlők kisegítő lehetőségeket.
- Az Azure AD Connect összes bejelentkezési metódusához hat összevonási felügyeleti feladat engedélyezése engedélyezett.  (Korábban csak az "AD FS TLS/SSL-tanúsítvány frissítése" feladat volt elérhető az összes bejelentkezéshez.)
- Figyelmeztetést adott hozzá, amikor a bejelentkezési módszert összevonásról PHS-re vagy PTA-ra módosítja, hogy az összes Azure AD-tartomány és felhasználó felügyelt hitelesítéssé lesz konvertálva.
- Eltávolította a jogkivonat-aláíró tanúsítványokat az "Azure AD és AD FS megbízhatóságának alaphelyzetbe állítása" feladatból, és hozzáadott egy külön alfeladatot a tanúsítványok frissítéséhez.
- Hozzáadott egy új összevonási kezelési feladatot, a "Tanúsítványok kezelése" nevű feladatot, amely alfeladatokat tartalmaz az AD FS-farm TLS- vagy jogkivonat-aláíró tanúsítványainak frissítéséhez.
- Hozzáadott egy új összevonási felügyeleti alfeladatot, az "Elsődleges kiszolgáló megadása" nevű alfeladatot, amely lehetővé teszi a rendszergazdák számára, hogy új elsődleges kiszolgálót adjanak meg az AD FS farmhoz.
- Hozzáadott egy új összevonási kezelési feladatot, a "Kiszolgálók kezelése" nevű feladatot, amely alfeladatokat tartalmaz az AD FS-kiszolgáló központi telepítéséhez, webalkalmazás-proxykiszolgáló telepítéséhez és az elsődleges kiszolgáló meghatározásához.
- Hozzáadott egy új összevonási kezelési feladatot,"Összevonási konfiguráció megtekintése" néven, amely az Aktuális AD FS-beállításokat jeleníti meg.  (Emiatt az AD FS-beállítások el lettek távolítva a "Megoldás áttekintése" oldalról.)

### <a name="fixed-issues"></a>Hibák kijavítva:
- Kijavítottuk a szinkronizálási hibát, amikor egy felhasználói objektum átveszi a megfelelő névjegyobjektumot,(pl. a felhasználó a saját vezetője).
- A súgó előugró ablakai mostantól a billentyűzet fókuszában jelennek meg.
- Automatikus frissítés esetén, ha bármely ütköző alkalmazás 6 órától fut, öld meg, és folytasd a frissítést.
- A címtárbővítmények kiválasztásakor objektumonként 100-ra korlátozhatja az ügyfél által kijelölhető attribútumok számát. Ez megakadályozza a hiba bekövetkezését az exportálás során, mivel az Azure objektumonként legfeljebb 100 bővítményattribútummal rendelkezik.
- Javítva egy hiba, hogy az AD Connectivity script robusztusabb legyen
- Javítva egy hiba, hogy az AADConnect egy meglévő Named Pipes WCF szolgáltatás használatával egy számítógépen legyen megbízhatóbb.
- Továbbfejlesztett diagnosztolás és hibaelhárítás olyan csoportházirendek körül, amelyek nem teszik lehetővé az ADSync szolgáltatás indítását az eredeti telepítéskor.
- Javítva egy hiba, amely helytelenül írta le a Windows rendszerű számítógép megjelenítendő nevét.
- Javítson ki egy hibát, amely szerint a Windows rendszerű számítógépek operációs rendszerének típusa helytelenül lett megírva.
- Javítva egy hiba, ahol a nem Windows 10-es számítógépek váratlanul szinkronizáltak. Vegye figyelembe, hogy a módosítás hatása az, hogy a korábban szinkronizált, nem Windows-10-es számítógépek törlődnek. Ez nem érinti a funkciókat, mivel a Windows-számítógépek szinkronizálása csak a hibrid Azure AD-tartományhoz való csatlakozáshoz használatos, amely csak Windows-10-eszközökesetén működik.
- Több új (belső) parancsmag került az ADSync PowerShell-modulhoz.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Ismert probléma van az Azure AD Connect korábbi verzióról 1.3.21.0-ra történő frissítésével kapcsolatban, ahol az O365-portál nem tükrözi a frissített verziót, annak ellenére, hogy az Azure AD Connect frissítése sikeresen megtörtént.
>
> A probléma megoldásához importálnia kell az **AdSync modult,** majd futtatnia kell a`Set-ADSyncDirSyncConfiguration` PowerShell-parancsmast az Azure AD Connect-kiszolgálón.  A következő lépéseket használhatja:
>
>1. Nyissa meg a PowerShellt rendszergazdai módban.
>2. Futtassa az `Import-Module "ADSync"` parancsot.
>3. Futtassa az `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` parancsot.
 
### <a name="release-status"></a>Kiadás állapota 

05/14/2019: Letölthető

### <a name="fixed-issues"></a>Hibák kijavítva: 

- Javítottuk a Microsoft Azure Active Directory Connect 1.3.20.0-s buildjében található jogosultságilletéktelen ségi biztonsági rést.  A biztonsági rés bizonyos feltételek mellett lehetővé teheti a támadó számára, hogy két PowerShell-parancsmakódot hajtson végre egy kiemelt jogosultságú fiók környezetében, és emelt szintű műveleteket hajtson végre.  Ez a biztonsági frissítés a parancsmagok letiltásával szünteti meg a problémát. További információt a [biztonsági frissítés című témakörben talál.](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Kiadás állapota 

04/24/2019: Letölthető

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések 

- A tartományfrissítés támogatásának hozzáadása 
- Exchange Mail nyilvános mappák szolgáltatás megy GA 
- A varázsló hibakezelésének javítása szolgáltatáshibák esetén 
- Figyelmeztető hivatkozás hozzáadása a Szinkronizálási szolgáltatáskezelő felhasználói felületén az összekötő tulajdonságai lapon. 
- Az Egyesített csoportok visszaírása szolgáltatás mostAntól GA 
- Továbbfejlesztett SSPR hibaüzenet, ha a tartományvezérlőn hiányzik egy LDAP-vezérlő 
- Hozzáadott diagnosztika a DCOM rendszerleíró adatbázis hibák telepítése során  
- Továbbfejlesztett nyomkövetés a PHS RPC hibák 
- EA creds engedélyezése gyermektartományból 
- Adatbázisnév beírásának engedélyezése a telepítés során (alapértelmezett név ADSync)
- Frissítsen Az ADAL 3.19.8-ra, hogy felvegye a Ping WS-Trust javítását, és támogatást adjon az új Azure-példányokhoz 
- Csoportszinkronizálási szabályok módosítása a samAccountName, a DomainNetbios és a DomainFQDN felhőbe történő áramlásához – a jogcímekhez szükséges 
- Módosított alapértelmezett szinkronizálási szabálykezelés – [itt](how-to-connect-fix-default-rules.md)olvashat bővebben.
- Hozzáadtunk egy windows-szolgáltatásként futó új ügynököt. Ez az "Admin Agent" nevű ügynök lehetővé teszi az Azure AD Connect kiszolgáló mélyebb távoli diagnosztikát, hogy segítsen a Microsoft Engineers-nek a hibaelhárításban egy támogatási eset megnyitásakor. Ez az ügynök alapértelmezés szerint nincs telepítve és engedélyezve.  Az ügynök telepítéséről és engedélyezéséről a [Mi az Azure AD Connect felügyeleti ügynök?](whatis-aadc-admin-agent.md) 
- Frissítve a végfelhasználói licencszerződés (EULA) 
- Az AD FS-t bejelentkezési típusként használó központi telepítések automatikus frissítési támogatása hozzáadva.  Ez is eltávolította az AD FS Azure AD függő entitás megbízhatóságának frissítésének követelményét a frissítési folyamat részeként. 
- Hozzáadott egy Azure AD megbízhatóságkezelési feladat, amely két lehetőséget biztosít: a megbízhatóság elemzése/frissítése és a megbízhatóság alaphelyzetbe állítása. 
- Módosította az AD FS Azure AD függő entitás megbízhatósági viselkedését, hogy mindig a -SupportMultipleDomain kapcsolót használja (beleértve a megbízhatóságot és az Azure AD tartomány frissítéseit). 
- Módosította az AD FS-farm új telepítésének viselkedését, hogy az .pfx tanúsítványt igényeljen az előre telepített tanúsítvány használatának lehetőségének eltávolításával.
- Frissítette az új AD FS farm-munkafolyamatot, hogy csak 1 AD FS és 1 WAP-kiszolgáló telepítését engedélyezhesse.  Minden további kiszolgáló a kezdeti telepítés után történik. 

### <a name="fixed-issues"></a>Hibák kijavítva: 

- Az ADSync szolgáltatás SQL-újracsatlakozási logikájának javítása 
- Fix, hogy tiszta telepítés egy üres SQL AOA DB 
- Ps Permissions parancsfájl javítása a GWB-engedélyek finomításához 
- Vss hibák javítása a LocalDB-vel  
- Félrevezető hibaüzenet kijavítása, ha az objektumtípus nincs hatókörben 
- Kijavítottuk azt a problémát, amely miatt az Azure AD PowerShell kiszolgálón történő telepítése összeszerelési ütközést okozhat az Azure AD Connecttel. 
- Javítottuk a PHS hibát az átmeneti kiszolgálón, amikor az összekötő hitelesítő adatai frissülnek a Szinkronizálási szolgáltatáskezelő felhasználói felületén. 
- Javítottuk a memóriavesztést 
- Egyéb automatikus frissítési javítások 
- Vegyes javítások exportálásához és meg nem erősített importfeldolgozáshoz 
- Kijavítottunk egy hibát a tartomány- és szervezetiegység-szűrés fordított perjelének kezelésével kapcsolatban 
- Kijavítottuk azt a problémát, amely miatt az ADSync szolgáltatás leállítása több mint 2 percet vesz igénybe, és problémát okoz a frissítés kor. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Kiadás állapota

12/18/2018: Letölthető

### <a name="fixed-issues"></a>Hibák kijavítva:

Ez a build frissíti a nem szabványos összekötők (például általános LDAP-összekötő és általános SQL-összekötő) szállított Azure AD Connect. A megfelelő összekötőkről további információt az 1.1.911.0-s verziójú [csatlakozók verziókiadási előzményei című](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history)témakörben talál.


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Kiadás állapota
12/11/2018: Letölthető

### <a name="fixed-issues"></a>Hibák kijavítva:
Ez a gyorsjavítás lehetővé teszi a felhasználó számára, hogy az eszköz visszaírásának engedélyezésekor a megadott erdőn belül kijelöljön egy céltartományt a RegisteredDevices tárolóhoz.  Az új Eszközbeállítások funkciót tartalmazó korábbi verziókban (1.1.819.0 – 1.2.68.0) a RegisteredDevices tároló helye az erdő gyökérterületére korlátozódott, és nem engedélyezte a gyermektartományokat.  Ez a korlátozás csak az új telepítéseken nyilvánult meg – a helyszíni frissítéseket ez nem érintette.  

Ha a frissített Eszközbeállítások funkciót tartalmazó buildet egy új kiszolgálóra telepítették, és az eszköz visszaírása engedélyezve volt, manuálisan kell megadnia a tároló helyét, ha nem szeretné, hogy az erdő gyökérben legyen.  Ehhez le kell tiltania az eszköz visszaírását, és újra engedélyeznie kell azt, amely lehetővé teszi a tároló helyének megadását a "Visszaíráserdő" oldalon.



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Kiadás állapota 

11/30/2018: Letölthető

### <a name="fixed-issues"></a>Hibák kijavítva:

Ez a gyorsjavítás javítja azt az ütközést, amelyben hitelesítési hiba léphet fel az MSOnline PowerShell-gyűjtemény modul nak a szinkronizálási kiszolgálón való független jelenléte miatt.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Kiadás állapota 

11/19/2018: Letölthető

### <a name="fixed-issues"></a>Hibák kijavítva:

Ez a gyorsjavítás az előző buildben regressziót javít, ahol a Jelszó-visszaírás sikertelen, ha egy HOZZÁTESZI tartományvezérlőt használ Windows Server 2008/R2 rendszeren.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Kiadás állapota 

10/25/2018: letölthető

 
### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések 


- Módosította az attribútum-visszaírás funkcióját, hogy a tárolt hangposta a várt módon működjön.  Bizonyos forgatókönyvek esetén az Azure AD felülírta az msExchUcVoicemailSettings attribútumot a null értékkel rendelkező visszaírás során.  Az Azure AD már nem törli az attribútum helyszíni értékét, ha a felhőbeli érték nincs beállítva.
- Az Azure AD Connect varázsló diagnosztikai adatait adta hozzá az Azure AD-hez kapcsolódó kapcsolódási problémák kivizsgálásához és azonosításához. Ugyanezek a diagnosztika is futtatható közvetlenül a PowerShell a Test- AdSyncAzureServiceConnectivity parancsmag használatával. 
- Az Azure AD Connect varázsló diagnosztikai adatait adta hozzá az AD-hez kapcsolódó kapcsolódási problémák vizsgálatához és azonosításához. Ugyanezek a diagnosztika is futtatható közvetlenül a PowerShell segítségével a Start-ConnectivityTools PowerShell modul Start-ConnectivityValidation függvény használatával.  További információ: [Mi az ADConnectivityTool PowerShell-modul?](how-to-connect-adconnectivitytools.md)
- Hozzáadott egy AD séma verzió előzetes ellenőrzése a hibrid Azure Active Directory-csatlakozáshoz és az eszközvisszaíráshoz 
- Módosította a Címtárkiterjesztés lap attribútumának keresését, hogy az ne legyen kitéve a kis- és nagybetűk nek.
-   Teljes mértékben támogatja a TLS 1.2-t. Ez a kiadás támogatja az összes többi protokoll le van tiltva, és csak a TLS 1.2 engedélyezve van a gépen, ahol az Azure AD Connect telepítve van.  További információ: [TLS 1.2 kényszerítés az Azure AD Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Hibák kijavítva:   

- Javítva egy hiba, amely miatt az Azure AD Connect frissítése sikertelen lett volna, ha az SQL Always On volt használatban. 
- Javítottunk egy hibát, amely helyesen elemezte a perjelet tartalmazó szervezeti egységek neveit. 
- Kijavítottuk azt a problémát, amely miatt az átmenő hitelesítés le lett tiltva egy tiszta telepítéshez átmeneti módban. 
- Javítva egy hiba, amely megakadályozta a PowerShell modul betöltését a hibaelhárító eszközök futtatásakor 
- Kijavítottunk egy hibát, amely megakadályozta, hogy az ügyfelek numerikus értékeket használjanak az állomásnév első karakterében. 
- Javítva egy hiba, amely miatt az Azure AD Connect érvénytelen partíciókat és tárolóválasztást engedélyezett 
- Javítva az "Érvénytelen jelszó" hibaüzenet, ha az asztali sso engedélyezve van. 
- Különböző hibajavítások az AD FS megbízhatósági kezeléséhez  
- Az Eszközvisszaírás konfigurálásakor - rögzítette a séma-ellenőrzést az msDs-DeviceContainer objektumosztály megkeresésére (ws2012 R2-en vezetve be)

 
## <a name="118820"></a>1.1.882.0  

9/7/2018: letölthető, nem lesz kiadás az automatikus frissítéshez 

### <a name="fixed-issues"></a>Hibák kijavítva:  

Az Azure AD Connect frissítése sikertelen, ha az SQL Always On Availability konfigurálva van az ADSync DB-hez. Ez a gyorsjavítás megoldja ezt a problémát, és lehetővé teszi a frissítés sikeresse. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Kiadás állapota

8/21/2018: Letölthető és automatikus frissítésre. 

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

- Az Azure AD Connect ping-federate integrációja már elérhető az általános elérhetőséghez. [További információ az Azure AD összeegyítéséről a Ping Federate segítségével](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Az Azure AD Connect most létrehozza az Azure AD megbízhatósági kapcsolat biztonsági mentését az AD FS-ben minden alkalommal, amikor egy frissítés történik, és egy külön fájlban tárolja, hogy szükség esetén könnyen visszaállíthassa. [Tudjon meg többet az azure AD Connect új funkcióiról és az Azure AD megbízhatósági kezeléséről.](https://aka.ms/fedtrustinaadconnect)
- Az új hibaelhárító eszköz segít az elsődleges e-mail cím módosításával és a fiók globális címlistából való elrejtésével kapcsolatos hibaelhárításban
- Az Azure AD Connect frissítve lett a legújabb SQL Server 2012 natív ügyféllel
- Amikor a "Felhasználói bejelentkezés módosítása" feladatban a jelszókivonat-szinkronizálásra vagy az átadó hitelesítésre vált felhasználói bejelentkezésre, alapértelmezés szerint engedélyezve van a Zökkenőmentes egyszeri bejelentkezés jelölőnégyzet.
- A Windows Server Essentials 2019 támogatása
- Az Azure AD Connect Health ügynök frissítve lett a legújabb 3.1.7.0-s verzióra
- A frissítés során, ha a telepítő az alapértelmezett szinkronizálási szabályok módosításait észleli, a rendszer figyelmeztetést kér a rendszergazdától, mielőtt felülírna a módosított szabályokat. Ez lehetővé teszi a felhasználó számára, hogy korrekciós intézkedéseket tegyen, és később folytassa. Régi viselkedés: Ha volt egy módosított out-of-box szabály, majd a manuális frissítés felülírta ezeket a szabályokat anélkül, hogy bármilyen figyelmeztetést a felhasználó és a szinkronizálási ütemező le van tiltva a felhasználó értesítése nélkül. Új viselkedés: A rendszer figyelmeztetést kér a felhasználótól, mielőtt felülírna a módosított használatra szóló szinkronizálási szabályokat. A felhasználó nak lehetősége lesz leállítani a frissítési folyamatot, és a korrekciós intézkedések meglépése után később folytatja.
- A FIPS-megfelelőségi probléma jobb kezelése, hibaüzenet küldése az MD5 kivonatgenerálásához fips-kompatibilis környezetben, valamint egy dokumentációra mutató hivatkozás, amely a probléma megoldását biztosítja.
- A felhasználói felület frissítése az összevonási feladatok javításához a varázslóban, amelyek most egy külön alcsoport alá tartoznak az összevonáshoz. 
- Az összes további összevonási további feladat egyetlen almenübe van csoportosítva a könnyű használat érdekében.
- Egy új megújult ADSyncConfig Posh modul (AdSyncConfig.psm1) új AD engedélyek funkciókkal költözött a régi ADSyncPrep.psm1 (amely lehet elavult hamarosan)

### <a name="fixed-issues"></a>Hibák kijavítva: 

- Javítva egy hiba, amely miatt az AAD Connect kiszolgáló magas PROCESSZOR-használatot mutatott a .NET 4.7.2-re való frissítés után.
- Javítva egy hiba, amely időnként hibaüzenetet eredményezett egy automatikusan megoldott SQL holtpont-probléma esetén
- Kijavítottunk néhány kisegítő lehetőséggel a Szinkronizálási szabályok szerkesztőjével és a Szinkronizálási szolgáltatáskezelővel kapcsolatban  
- Javítva egy hiba, ahol az Azure AD Connect nem tud rendszerleíró beállítási információkat
- Javítva egy hiba, amely problémákat okozott, amikor a felhasználó előre/vissza megy a varázslóban
- Javítva egy hiba, amely megakadályozta, hogy hiba történjen a varázsló helytelen többszálas átadása miatt
- Ha a csoportszinkronizálási szűrési lap LDAP-hibát észlel a biztonsági csoportok feloldásakor, az Azure AD Connect most teljes hűséggel adja vissza a kivételt.  Az átirányítási kivétel kiváltó oka még mindig ismeretlen, és egy másik hiba foglalkozik vele.
-  Kijavítottunk egy hibát, amely miatt az STK- és NGC-kulcsok (ms-DS-KeyCredentialLink attribútum a WHfB felhasználói/eszközobjektumain) engedélyei nem voltak megfelelően beállítva.     
- Javítva egy hiba, ahol a "Set-ADSyncRestrictedPermissions" nem volt megfelelően hívva
-  A Csoportvisszaírás engedélyezésének támogatása az AADConnect telepítővarázslójában
- Amikor a bejelentkezési módszert jelszókivonat-szinkronizálásról AD FS-re módosítja, a jelszókivonat-szinkronizálás nem volt letiltva.
- Az IPv6-címek ellenőrzése hozzáadva az AD FS-konfigurációban
- Frissítve az értesítési üzenet, amely tájékoztatja, hogy létezik egy meglévő konfiguráció.
- Az eszközvisszaírás nem észleli a nem megbízható erdőben lévő tárolót. Ezt frissítettük, hogy jobb hibaüzenetet és a megfelelő dokumentációra mutató linket biztosítson
- A szervezeti egység kijelölésének megszüntetéséhez, majd az adott szervezeti egységnek megfelelő szinkronizálási/visszaírási hibákat okoz. Ez megváltozott, hogy egy érthetőbb hibaüzenetet hozzon létre.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Kiadás állapota

5/14/2018: Automatikus frissítésre és letöltésre elérhető.

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

Új funkciók és fejlesztések

- Ez a kiadás tartalmazza a PingFederate azure AD Connect integrációjának nyilvános előzetes verzióját. Ezzel a kiadással az ügyfelek egyszerűen és megbízhatóan konfigurálhatják az Azure Active Directory-környezetüket, hogy a PingFederate-et használják összevonási szolgáltatóként. Ha többet szeretne megtudni az új funkció használatáról, kérjük, látogasson el [online dokumentációnkba.](plan-connect-user-signin.md#federation-with-pingfederate) 
- Frissítette az Azure AD Connect varázsló hibaelhárító segédprogramját, ahol most további hibaforgatókönyveket elemez, például csatolt postaládákat és AD dinamikus csoportokat. A hibaelhárító segédprogramról [itt](tshoot-connect-objectsync.md)olvashat bővebben.
- Az eszköz-visszaírási konfiguráció mostantól kizárólag az Azure AD Connect varázslón belül kezelhető.
- Egy új PowerShell modul nevű ADSyncTools.psm1 egészül ki, hogy lehet használni, hogy elhárítsa az SQL Connectivity problémák és egyéb hibaelhárító segédprogramok. Tudjon meg többet az ADSyncTools modul [itt](tshoot-connect-tshoot-sql-connectivity.md). 
- Egy új, "Eszközbeállítások konfigurálása" feladatot adtunk hozzá. A feladat segítségével a következő két műveletet konfigurálhatja: 
  - **Hibrid Azure AD-csatlakozás:** Ha a környezet rendelkezik egy helyszíni AD lábnyom, és azt is szeretné kihasználni az Azure Active Directory által biztosított képességek, hibrid Azure AD-illesztésű eszközök valósíthatja meg. Ezek olyan eszközök, amelyek a helyszíni Active Directoryhoz és az Azure Active Directoryhoz is csatlakoznak.
  - **Eszközvisszaírás:** Az eszközvisszaírás az AD FS (2012 R2 vagy újabb) védett eszközök (2012 R2 vagy újabb) eszközökön alapuló feltételes hozzáférés engedélyezésére szolgál

    >[!NOTE] 
    > - Az eszközvisszaírás engedélyezése a Szinkronizálási beállítások testreszabása párbeszédpanelen szürkén jelenik meg. 
    > -  Az ADPrep PowerShell-modulja ezzel a kiadással elavult.



### <a name="fixed-issues"></a>Hibák kijavítva: 

- Ez a kiadás frissíti az SQL Server Express telepítését az SQL Server 2012 SP4 szervizcsomagra, amely többek között számos biztonsági rés javítását biztosítja.  Az SQL Server 2012 SP4 szervizcsomaggal kapcsolatos további információkért tekintse meg [itt.](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information)
- Szinkronizálási szabály feldolgozása: a csatlakozási feltétel nélküli kimenő illesztési szinkronizálási szabályokat vissza kell fogni, ha a szülőszinkronizálási szabály már nem alkalmazható
- Számos kisegítő lehetőség javítómegoldás történt a Szinkronizálási szolgáltatáskezelő felhasználói felületén és a Szinkronizálási szabályok szerkesztőben
- Azure AD Connect varázsló: Hiba az AD-összekötő fiók létrehozásakor, ha az Azure AD Connect munkacsoportban van
- Azure AD Connect varázsló: Az Azure AD bejelentkezési lapon megjelenik az ellenőrzés jelölőnégyzet, ha eltérés van az AD-tartományokban és az Azure AD-ellenőrzött tartományokban
- A PowerShell automatikus frissítése az automatikus frissítési állapot megfelelő beállításához bizonyos esetekben az automatikus frissítési kísérlet után.
- Azure AD Connect varázsló: Frissített telemetriai adatok a korábban hiányzó információk rögzítéséhez
- Az Azure AD Connect varázsló: A következő módosítások történtek, amikor a **Felhasználói bejelentkezés módosítása** feladattal vált át az AD FS-ről az átmenő hitelesítésre:
    - Az átmenő hitelesítési ügynök telepítve van az Azure AD Connect kiszolgálón, és az áteresztő hitelesítés funkció engedélyezve van, mielőtt a tartomány(oka)t összevontról felügyeltre konvertálnánk.
    - A felhasználók már nem konvertálódnak összevontról felügyeltre. Csak a tartomány(ok) konvertálódnak.
- Az Azure AD Connect varázsló: Az AD FS multi domain regex nem megfelelő, ha a felhasználó upn speciális karakterrel rendelkezik a Regex frissítéssel, amely támogatja a speciális karaktereket
- Az Azure AD Connect varázsló: A hamis "Forráshorgony attribútum konfigurálása" üzenet eltávolítása, ha nincs változás 
- Azure AD Connect varázsló: Az AD FS támogatása a kettős összevonási forgatókönyvhöz
- Azure AD Connect varázsló: Az AD FS-jogcímek nem frissülnek a hozzáadott tartományhoz, amikor egy felügyelt tartományt összevontra konvertálnak
- Azure AD Connect varázsló: A telepített csomagok észlelése során elavult Dirsync/Azure AD Sync/Azure AD Connect kapcsolódó termékeket találunk. Most megpróbáljuk eltávolítani az elavult termékeket.
- Az Azure AD Connect varázsló: Hibaleképezés javítása az átmenő hitelesítési ügynök telepítése esetén
- Azure AD Connect varázsló: Eltávolítva a "Konfiguráció" tárolót a tartományszervezeti egység szűrési lapjáról
- Sync Engine telepítése: távolítsa el a felesleges örökölt logika, hogy néha nem sikerült a Sync Engine telepítése msi
- Az Azure AD Connect varázsló: A jelszókivonat-szinkronizálás választható szolgáltatások lapján az előugró súgó szövegének javítása
- Szinkronizálási motor futásideje: Javítsa ki azt a forgatókönyvet, amelyben egy CS-objektum importált törlési és szinkronizálási szabályok kísérlik meg újra kiépíteni az objektumot.
- Szinkronizálási motor futásideje: Súgóhivatkozás hozzáadása az online kapcsolathibaelhárítási útmutatóhoz az importálási hiba eseménynaplójába
- Szinkronizálási motor futásideje: A Szinkronizálásütemező csökkentett memóriahasználata az összekötők számbavételekor
- Azure AD Connect varázsló: Probléma megoldása egy egyéni szinkronizálási szolgáltatásfiók megoldásában, amely nem rendelkezik AD olvasási jogosultságokkal
- Azure AD Connect varázsló: A tartomány- és szervezetiegység-szűrési beállítások naplózásának javítása
- Azure AD Connect varázsló: Az AD FS alapértelmezett jogcímek hozzáadása az MFA-forgatókönyvhöz létrehozott összevonási bizalmi kapcsolathoz
- Azure AD Connect varázsló: AD FS deploy WAP: A kiszolgáló hozzáadása nem tudja használni az új tanúsítványt
- Azure AD Connect varázsló: DSSO-kivétel, ha az onPremCredentials nincs inicializálva egy tartományhoz 
- Az AD distinguishedName attribútumot előnyösen kell az Active User objektummal folyatni.
- Javítva, hogy egy kozmetikai hiba volt az első OOB szinkronizálási szabály prioritása, 100 helyett 99-re volt állítva



## <a name="117510"></a>1.1.751.0
Állapot 4/12/2018: Csak letölthető

>[!NOTE]
>Ez a kiadás az Azure AD Connect gyorsjavítása

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
#### <a name="fixed-issues"></a>Hibák kijavítva:
Kijavított egy probléma volt az automatikus Azure-példány felderítése a kínai bérlők időnként sikertelen volt.  

### <a name="ad-fs-management"></a>AD FS kezelése
#### <a name="fixed-issues"></a>Hibák kijavítva:

Hiba történt a konfigurációs újrapróbálkozási logikában, ami egy ArgumentException-et eredményezett, amely szerint "egy ugyanazzal a kulccsal rendelkező elem már hozzá lett adva".  Ez az összes újrapróbálkozási művelet sikertelensedéséhez vezetne.

## <a name="117500"></a>1.1.750.0
Állapot 3/22/2018: Automatikus frissítésre és letöltésre alkalmas.
>[!NOTE]
>Amikor az új verzióra való frissítés befejeződik, automatikusan elindítja az Azure AD-összekötő teljes szinkronizálását és teljes importálását, valamint az AD-összekötő teljes szinkronizálását. Mivel ez eltarthat egy ideig, az Azure AD Connect-környezet méretétől függően győződjön meg arról, hogy megtette a szükséges lépéseket ennek támogatásához, vagy távol tartja magát a frissítéstől, amíg nem talál egy megfelelő pillanatot erre.

>[!NOTE]
>"Az automatikus frissítési funkció helytelenül le lett tiltva egyes bérlők esetében, akik az 1.1.524.0-nál később telepített buildeket telepítették. Annak érdekében, hogy az Azure AD Connect-példány továbbra is jogosult legyen az automatikus frissítésre, futtassa a következő PowerShell-parancsmast: "Set-ADSyncAutoUpgrade -AutoupGradeState Enabled"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Hibák kijavítva:

* A Set-ADSyncAutoUpgrade parancsmag korábban blokkolja az automatikus frissítést, ha az automatikus frissítés állapota Felfüggesztve. Ez a funkció most megváltozott, így nem blokkolja a jövőbeli buildek automatikus frissítését.
* A "Jelszó-szinkronizálás" **felhasználói bejelentkezési** oldal "Jelszókivonat-szinkronizálás" beállítását "Jelszókivonat-szinkronizálás" beállításra módosította.  Az Azure AD Connect a jelszókitagokat szinkronizálja, nem a jelszavakat, így ez igazodik a ténylegesen előforduló eseményekhez.  További információ: [Jelszókivonat-szinkronizálás megvalósítása az Azure AD Connect szinkronizálásával című témakörben.](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Állapot: Kiadva a vevők számára

>[!NOTE]
>Amikor az új verzióra való frissítés befejeződik, automatikusan elindítja az Azure AD-összekötő teljes szinkronizálását és teljes importálását, valamint az AD-összekötő teljes szinkronizálását. Mivel ez eltarthat egy ideig, az Azure AD Connect-környezet méretétől függően győződjön meg arról, hogy megtette a szükséges lépéseket ennek támogatásához, vagy távol tartja magát a frissítéstől, amíg nem talál egy megfelelő pillanatot erre.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Hibák kijavítva:
* A következő oldalra való váltáskor javítsa ki az időzítési ablakot a partíciószűrés lapjának háttérfeladatain.

* Kijavítottunk egy hibát, amely az Access megsértését okozta a ConfigDB egyéni művelet során.

* Javítva egy hiba, hogy visszaszerezze az SQL-kapcsolat időtúllépés.

* Kijavítottunk egy hibát, amely miatt a SAN helyettesítő karakterekkel rendelkező tanúsítványok nem tudtak megegyezésű ellenőrzésen.

* Kijavítottunk egy hibát, amely miatt a miiserver.exe összeomlik az Azure AD-összekötő exportálása során.

* Javítva egy hiba, amely hibás jelszó kísérlet bejelentkezett a DC futtatásakor az Azure AD Connect varázsló a konfiguráció módosítása.


#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* Az általános adatvédelmi rendelet (GDPR) adatvédelmi beállításainak hozzáadása.  További információkért lásd a [cikket itt](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* alkalmazás telemetria - admin lehet kapcsolni ezt az osztályt az adatok be / ki lesz

* Azure AD Health-adatok – a rendszergazdának fel kell keresnie az állapotfigyelő portált az állapotbeállítások szabályozásához.
   A szolgáltatásházirend módosítása után az ügynökök elolvassák és kényszerítik azt.

* Eszköz-visszaírási konfigurációs műveletek és folyamatjelző sáv hozzáadása az oldal inicializálásához

* Továbbfejlesztett általános diagnosztika HTML-jelentéssel és teljes adatgyűjtéssel zip-text / HTML jelentésben

* Az automatikus frissítés megbízhatóságának javítása és további telemetriai adatok hozzáadása a kiszolgáló állapotának meghatározásához

* Az AD Connector-fiók kiemelt jogosultságú fiókjai számára elérhető engedélyek korlátozása

  * Új telepítések esetén a varázsló korlátozza az MSOL-fiókhoz szükséges jogosultságokkal rendelkező fiókok engedélyeit az MSOL-fiók létrehozása után.

A változások a következőket teszik:
1. Expressz telepítések
2. Egyéni telepítések automatikus létrehozási fiókkal
3. Módosította a telepítőt, hogy ne igényeljen SA-jogosultságot az Azure AD Connect tiszta telepítéséhez

* Hozzáadott egy új segédprogramot egy adott objektum szinkronizálási problémáinak elhárításához. Az Azure AD Connect varázsló "Objektumszinkronizálás hibaelhárítása" beállításával érhető el további feladatok elhárítása. Jelenleg a segédprogram a következőket ellenőrzi:

  * UserPrincipalName eltérés a szinkronizált felhasználói objektum és a felhasználói fiók az Azure AD tenant.
  * Ha az objektumot tartományszűrés miatt szűrik a szinkronizálásból
  * Ha az objektumot a szervezeti egység (OU) szűrése miatt szűrik a szinkronizálásról

* Hozzáadott egy új segédprogramot a helyszíni Active Directoryban egy adott felhasználói fiókhoz tárolt aktuális jelszókivonat szinkronizálásához.

A segédprogram nem igényel jelszómódosítást. Az Azure AD Connect varázsló "Jelszókivonat-szinkronizálás hibaelhárítása" beállításával érhető el további feladatok elhárítása.






## <a name="116540"></a>1.1.654.0
Állapot: december 12, 2017

>[!NOTE]
>Ez a kiadás az Azure AD Connect biztonsági gyorsjavítása

### <a name="azure-ad-connect"></a>Azure AD Connect
Az Azure AD Connect 1.1.654.0-s (és azt követő) verziójának javítása hozzáadva győződjön meg arról, hogy az [AD DS-fiókhoz való hozzáférés zárolása](#lock) című szakaszban ismertetett ajánlott engedélymódosítások automatikusan érvénybe lépnek, amikor az Azure AD Connect létrehozza az AD DS-fiókot. 

- Az Azure AD Connect beállításakor a telepítő rendszergazda vagy egy meglévő Active AD DS-fiókot biztosít, vagy hagyja, hogy az Azure AD Connect automatikusan hozza létre a fiókot. Az engedély módosításai automatikusan vonatkoznak az Azure AD Connect által a telepítés során létrehozott AD DS-fiókra. Ezek nem vonatkoznak a telepítő rendszergazda által biztosított meglévő AD DS-fiókra.
- Azon ügyfelek számára, akik az Azure AD Connect egy régebbi verziójáról 1.1.654.0-ra (vagy azt követően) frissítettek, az engedélymódosítások visszamenőleges hatállyal nem lesznek visszamenőlegesen alkalmazva a frissítés előtt létrehozott meglévő AD DS-fiókokra. Ezek csak a frissítés után létrehozott új AD DS-fiókokra lesznek alkalmazva. Ez akkor fordul elő, ha új AD-erdőket ad hozzá az Azure AD-hez szinkronizálni.

>[!NOTE]
>Ez a kiadás csak az Azure AD Connect új telepítései esetén szünteti meg a biztonsági rést, ahol a telepítési folyamat létrehozza a szolgáltatásfiókot. Meglévő telepítések esetén, illetve olyan esetekben, amikor a fiókot saját maga adja meg, győződjön meg arról, hogy a biztonsági rés nem létezik.

#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a>Hozzáférés zárolása az AD DS-fiókhoz
Zárolja az AD DS-fiókhoz való hozzáférést a helyszíni AD-ben a következő engedélymódosítások végrehajtásával:  

*   Öröklődés letiltása a megadott objektumon
*   Távolítsa el az adott objektum összes aces-ét, kivéve a SELF-re jellemző ACEs-eket. Azt akarjuk, hogy az alapértelmezett engedélyek ép, amikor a SELF.
*   Rendelje hozzá a következő speciális engedélyeket:

Típus     | Név                          | Hozzáférés               | Érvényesség
---------|-------------------------------|----------------------|--------------|
Engedélyezés    | RENDSZER                        | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Vállalati rendszergazdák             | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Tartományi rendszergazdák                 | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Rendszergazdák                | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Lista tartalma        | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Az összes tulajdonság olvasása  | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Olvasási engedélyek     | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Lista tartalma        | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Az összes tulajdonság olvasása  | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Olvasási engedélyek     | Ez az objektum  |

Az AD DS-fiók beállításainak szigorításához futtathatja [ezt a PowerShell-parancsfájlt.](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978) A PowerShell-parancsfájl hozzárendeli a fent említett engedélyeket az AD DS-fiókhoz.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell-parancsfájl egy már meglévő szolgáltatásfiók meghúzásához

A PowerShell-parancsfájl használatához alkalmazza ezeket a beállításokat egy már meglévő AD DS-fiókra (a szervezet által biztosított vagy az Azure AD Connect egy korábbi telepítése által létrehozott éterre, töltse le a parancsfájlt a megadott hivatkozásról.

##### <a name="usage"></a>Használat

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Ahol 

**$ObjectDN** = Az az Active Directory-fiók, amelynek engedélyeit meg kell szigorítani.

**$Credential** = Rendszergazdai hitelesítő adatok, amely rendelkezik a $ObjectDN fiók engedélyeinek korlátozásához szükséges jogosultságokkal. Ezeket a jogosultságokat általában a vállalati vagy tartományi rendszergazda tartja. A fiókfelkutatási hibák elkerülése érdekében használja a rendszergazdai fiók teljesen minősített tartománynevét. Példa: contoso.com\admin.

>[!NOTE] 
>$credential. A felhasználónévnek FQDN\felhasználónév formátumban kell lennie. Példa: contoso.com\admin 

##### <a name="example"></a>Példa:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>A biztonsági rés jogosulatlan hozzáférésre használt?

Ha meg szeretné tudni, hogy a biztonsági rés az Azure AD Connect-konfiguráció sérülésére szolgált-e, ellenőrizze a szolgáltatásfiók utolsó jelszó-visszaállítási dátumát.  Ha az időbélyeg váratlan, további vizsgálatot kell végezni az eseménynaplón keresztül, hogy a jelszó-visszaállítási esemény, kell végezni.

További információt a [Microsoft 4056318 számú biztonsági tanácsadója című témakörben talál.](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Állapot: október 27, 2017

>[!NOTE]
>Ez a build nem érhető el az ügyfelek számára az Azure AD Connect automatikus frissítési szolgáltatáson keresztül.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Hiba kijavítva: 
* Kijavítottuk az Azure AD Connect és az Azure AD Connect állapotügynök (szinkronizáláshoz) közötti verziókompatibilitási problémát. Ez a probléma azokat az ügyfeleket érinti, akik az Azure AD Connect et az 1.1.647.0-s verzióra való helyben frissítik, de jelenleg a Health Agent 3.0.127.0-s verzióját használják. A frissítés után az állapotügynök már nem küldhet állapotadatokat az Azure AD Connect szinkronizálási szolgáltatásról az Azure AD Health Service szolgáltatásnak. Ezzel a javítással az Állapotügynök 3.0.129.0-s verziója települ az Azure AD Connect helybeni frissítése során. Az Health Agent 3.0.129.0-s verziója nem rendelkezik kompatibilitási problémával az Azure AD Connect 1.1.649.0-s verziójával.


## <a name="116470"></a>1.1.647.0
Állapot: október 19 2017

> [!IMPORTANT]
> Ismert kompatibilitási probléma van az Azure AD Connect 1.1.647.0-s verziója és az Azure AD Connect Health Agent (szinkronizáláshoz) 3.0.127.0-s verziója között. Ez a probléma megakadályozza, hogy az állapotügynök az Azure AD Connect szinkronizálási szolgáltatás állapotadatait (beleértve az objektumszinkronizálási hibákat és az előzményadatok futtatását) az Azure AD Health Service szolgáltatásnak küldje el. Mielőtt manuálisan frissítené az Azure AD Connect központi telepítését az 1.1.647.0-s verzióra, ellenőrizze az Azure AD Connect állapotügynök aktuális verzióját az Azure AD Connect-kiszolgálón telepítve. Ezt megteheti a *Vezérlőpulton található : Programok telepítése* és alkalmazás, a Microsoft Azure AD Connect Health Agent for Sync (Programok telepítése és a Microsoft Azure *AD Connect Health Agent for Sync)* című lapban. Ha a verzió 3.0.127.0, javasoljuk, hogy várja meg a következő Azure AD Connect verzió elérhetővé a frissítés előtt. Ha az állapotügynök verziója nem 3.0.127.0, akkor rendben van, hogy folytassa a manuális, helybeni frissítés. Vegye figyelembe, hogy ez a probléma nem érinti a swing-frissítést, illetve azokat az ügyfeleket, akik az Azure AD Connect új telepítését hajtják végre.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Hibák kijavítva:
* Kijavítottunk egy problémát a *Felhasználói bejelentkezés módosítása* feladattal az Azure AD Connect varázslóban:

  * A probléma akkor fordul elő, ha az Azure AD Connect meglévő központi telepítése engedélyezve van a jelszó-szinkronizálás **funkcióval,** és a felhasználói bejelentkezési módszert *átadó hitelesítésként*próbálja beállítani. A módosítás alkalmazása előtt a varázsló helytelenül jeleníti meg a "*Jelszó-szinkronizálás letiltása*" parancssor. A jelszó-szinkronizálás azonban a módosítás alkalmazása után is engedélyezve marad. Ezzel a javítással a varázsló már nem jeleníti meg a kérdést.

  * A varázsló szándékosan nem tiltja le a jelszó-szinkronizálást, amikor frissíti a felhasználói bejelentkezési módszert a *Felhasználói bejelentkezésmódosítása* feladattal. Ez a jelszó-szinkronizálást megtartani kívánó ügyfelek megzavarásának elkerülése, még akkor is, ha elsődleges felhasználói bejelentkezési módszerként engedélyezik az átmenő hitelesítést vagy az összevonást.
  
  * Ha a felhasználó bejelentkezési metódusának frissítése után le szeretné tiltani a jelszó-szinkronizálást, akkor a varázslóban végre kell hajtania a *Szinkronizálás konfigurációjának testreszabása* feladatot. Amikor a *Választható szolgáltatások* lapra lép, törölje a jelet a *Jelszó-szinkronizálás jelölőnégyzetből.*
  
  * Vegye figyelembe, hogy ugyanez a probléma akkor is előfordul, ha megpróbálja engedélyezni/letiltani a zökkenőmentes egyszeri bejelentkezést. Pontosabban rendelkezik egy meglévő Azure AD Connect-telepítéssel, amelyen engedélyezve van a jelszó-szinkronizálás, és a felhasználói bejelentkezési módszer már *átmenő hitelesítésként*van konfigurálva. A *Felhasználói bejelentkezés módosítása* feladat használatával megpróbálja becsekkolni vagy törölje a jelet a *Zökkenőmentes egyszeri bejelentkezés engedélyezése* beállításból, miközben a felhasználói bejelentkezési módszer "Áthaladási hitelesítés" néven marad konfigurálva. A módosítás alkalmazása előtt a varázsló helytelenül jeleníti meg a "*Jelszó-szinkronizálás letiltása*" parancssor. A jelszó-szinkronizálás azonban a módosítás alkalmazása után is engedélyezve marad. Ezzel a javítással a varázsló már nem jeleníti meg a kérdést.

* Kijavítottunk egy problémát a *Felhasználói bejelentkezés módosítása* feladattal az Azure AD Connect varázslóban:

  * A probléma akkor fordul elő, ha az Azure AD Connect meglévő központi telepítése **le van tiltva**a jelszó-szinkronizálás letiltásával, és a felhasználói bejelentkezési módszert *átadó hitelesítésként*próbálja beállítani. A módosítás alkalmazásakor a varázsló engedélyezi az átmenő hitelesítést és a jelszó-szinkronizálást is. Ezzel a javítással a varázsló már nem engedélyezi a jelszó-szinkronizálást.

  * Korábban a jelszó-szinkronizálás előfeltétel volt az átmenő hitelesítés engedélyezéséhez. Ha a felhasználói bejelentkezési módszert *átadó hitelesítésként állítja*be, a varázsló mind az átadó hitelesítést, mind a jelszó-szinkronizálást engedélyezi. A közelmúltban a jelszó-szinkronizálást előfeltételként eltávolították. Az Azure AD Connect 1.1.557.0-s verziójának részeként az Azure AD Connect nem engedélyezte a jelszó-szinkronizálást, amikor a felhasználói bejelentkezési módszert *átadó hitelesítésként állítja*be. A módosítás azonban csak az Azure AD Connect telepítésére vonatkozott. Ezzel a javítással ugyanaz a módosítás vonatkozik a *Felhasználó bejelentkezési feladatának módosítására* is.
  
  * Vegye figyelembe, hogy ugyanez a probléma akkor is előfordul, ha megpróbálja engedélyezni/letiltani a zökkenőmentes egyszeri bejelentkezést. Pontosabban rendelkezik egy meglévő Azure AD Connect-telepítéssel, amelyben a jelszó-szinkronizálás le van tiltva, és a felhasználói bejelentkezési módszer már *átmenő hitelesítésként*van konfigurálva. A *Felhasználói bejelentkezés módosítása* feladat használatával megpróbálja becsekkolni vagy törölje a jelet a *Zökkenőmentes egyszeri bejelentkezés engedélyezése* beállításból, miközben a felhasználói bejelentkezési módszer "Áthaladási hitelesítés" néven marad konfigurálva. A módosítás alkalmazásakor a varázsló engedélyezi a jelszó-szinkronizálást. Ezzel a javítással a varázsló már nem engedélyezi a jelszó-szinkronizálást. 

* Kijavítottuk azt a hibát, amely miatt az Azure AD Connect frissítése a következő hiba miatt meghiúsult: "*Nem lehet frissíteni a szinkronizálási szolgáltatást".* Továbbá a szinkronizálási szolgáltatás már nem indítható eseményhibával:*" A szolgáltatás nem tudott elindulni, mert az adatbázis verziója újabb, mint a telepített bináris fájlok verziója".* A probléma akkor fordul elő, ha a frissítést végző rendszergazda nem rendelkezik rendszergazdai jogosultsággal az Azure AD Connect által használt SQL-kiszolgálón. Ezzel a javítással az Azure AD Connect csak a rendszergazda számára, hogy db_owner jogosultsággal rendelkezik az ADSync-adatbázis frissítés során.

* Kijavítottunk egy Azure AD Connect frissítési problémát, amely a [zökkenőmentes egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)engedélyező ügyfeleket érintette. Az Azure AD Connect frissítése után a zökkenőmentes egyszeri bejelentkezés helytelenül jelenik meg letiltva az Azure AD Connect varázslóban, annak ellenére, hogy a funkció továbbra is engedélyezve és teljesen működőképes marad. Ezzel a javítással a szolgáltatás mostantól helyesen jelenik meg, ahogy az engedélyezve van a varázslóban.

* Kijavítottuk azt a hibát, amely miatt az Azure AD Connect varázsló mindig a "*Source Anchor konfigurálása*" parancs sorát jelenítette meg a *Konfigurálásra kész* lapon, még akkor is, ha nem történt módosítás a forrásszerkesztővel kapcsolatban.

* Az Azure AD Connect manuális helyszíni frissítése során az ügyfélnek meg kell adnia a megfelelő Azure AD-bérlő globális rendszergazdai hitelesítő adatait. Korábban a frissítés folytatható annak ellenére, hogy a globális rendszergazda hitelesítő adatai egy másik Azure AD-bérlőhöz tartoztak. Bár úgy tűnik, hogy a frissítés sikeresen befejeződött, bizonyos konfigurációk nem megfelelően maradnak meg a frissítéssel. Ezzel a módosítással a varázsló megakadályozza, hogy a frissítés folytatódik, ha a megadott hitelesítő adatok nem egyeznek meg az Azure AD-bérlő.

* Eltávolította a redundáns logikát, amely szükségtelenül újraindította az Azure AD Connect Health szolgáltatást a manuális frissítés kezdetén.


#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Logikával egyszerűsíti az Azure AD Connect és a Microsoft Germany Cloud beállításához szükséges lépéseket. Korábban az Azure AD Connect-kiszolgálón bizonyos beállításkulcsokat kell frissítenie ahhoz, hogy az megfelelően működjön a Microsoft Germany Cloud szolgáltatással, a jelen cikkben leírtak szerint. Mostantól az Azure AD Connect automatikusan észleli, ha a bérlő a Microsoft Germany Cloud-ban található a telepítés során megadott globális rendszergazdai hitelesítő adatok alapján.

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
> [!NOTE]
> Megjegyzés: A szinkronizálási szolgáltatás rendelkezik egy WMI-felülettel, amely lehetővé teszi saját egyéni ütemező fejlesztését. Ez a felület most elavult, és el lesz távolítva az Azure AD Connect 2018. Azoknak az ügyfeleknek, akik testre szeretnék szabni a szinkronizálási ütemezést, a [beépített ütemezőt](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler)kell használniuk.

#### <a name="fixed-issues"></a>Hibák kijavítva:
* Amikor az Azure AD Connect varázsló létrehozza a helyszíni Active Directory módosításai szinkronizálásához szükséges AD-összekötő fiókot, nem rendeli hozzá megfelelően a fiókot a Nyilvános mappa objektumok olvasásához szükséges engedélyhez. Ez a probléma az Express és az Egyéni telepítést egyaránt érinti. Ez a módosítás megoldja a problémát.

* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect varázsló hibaelhárítási lapja nem jelenett meg megfelelően a Windows Server 2016-ról futó rendszergazdák számára.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* A Jelszó-szinkronizálás hibaelhárítása az Azure AD Connect varázsló hibaelhárítási lapján, a hibaelhárítási lap most visszaadja a tartomány-specifikus állapot.

* Korábban, ha megpróbálta engedélyezni a jelszókivonat-szinkronizálást, az Azure AD Connect nem ellenőrzi, hogy az AD-összekötő fiók rendelkezik-e a helyszíni AD jelszókivonatok szinkronizálásához szükséges engedélyekkel. Most az Azure AD Connect varázsló ellenőrzi, és figyelmezteti, ha az AD-összekötő fiók nem rendelkezik a megfelelő engedélyekkel.

### <a name="ad-fs-management"></a>AD FS kezelése
#### <a name="fixed-issue"></a>Hiba kijavítva: 
* Kijavítottuk az [ms-DS-ConsistencyGuid forrásszerkesztőként való](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) használatával kapcsolatos problémát. Ez a probléma azokat az ügyfeleket érinti, akik az *AD FS-sel* konfigurált összevonást felhasználói bejelentkezési módszerként konfigurálták. Amikor a varázslóban *a Forráshorgony konfigurálása* feladatot hajtja végre, az Azure AD Connect a *ms-DS-ConsistencyGuid azonosítót használja az egyik nem módosítható azonosító forrásattribútumaként. A módosítás részeként az Azure AD Connect megpróbálja frissíteni az AD FS-ben az ImmutableId jogcímszabályait. Ez a lépés azonban nem sikerült, mert az Azure AD Connect nem rendelkezett az AD FS konfigurálásához szükséges rendszergazdai hitelesítő adatokkal. Ezzel a javítással az Azure AD Connect most kéri, hogy adja meg az AD FS rendszergazdai hitelesítő adatait, amikor végrehajtja a *Forrásszerkesztő* feladat végrehajtása.



## <a name="116140"></a>1.1.614.0
Állapot: szeptember 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Ismert problémák
* Van egy ismert probléma, amely miatt az Azure AD Connect frissítése sikertelen a következő hiba :*"Nem lehet frissíteni a szinkronizálási szolgáltatás*". Továbbá a szinkronizálási szolgáltatás már nem indítható eseményhibával:*" A szolgáltatás nem tudott elindulni, mert az adatbázis verziója újabb, mint a telepített bináris fájlok verziója".* A probléma akkor fordul elő, ha a frissítést végző rendszergazda nem rendelkezik rendszergazdai jogosultsággal az Azure AD Connect által használt SQL-kiszolgálón. A Dbo engedélyek nem elegendőek.

* Az Azure AD Connect frissítéssel kapcsolatban ismert probléma van, amely azokat az ügyfeleket érinti, akik engedélyezték [a zökkenőmentes egyszeri bejelentkezést.](how-to-connect-sso.md) Az Azure AD Connect frissítése után a szolgáltatás letiltottként jelenik meg a varázslóban, annak ellenére, hogy a funkció továbbra is engedélyezve van. A probléma javítása a későbbi kiadásban lesz megadva. Azok a felhasználók, akik aggódnak a megjelenítési probléma miatt, manuálisan kijavíthatják azt, ha engedélyezik a zökkenőmentes egyszeri bejelentkezést a varázslóban.

#### <a name="fixed-issues"></a>Hibák kijavítva:
* Kijavítottuk azt a problémát, amely megakadályozta, hogy az Azure AD Connect frissítse a jogcímszabályokat a helyszíni AD FS szolgáltatásban, miközben lehetővé tette az [ms-DS-ConsistencyGuid forráshorgonyként](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) való használatát. A probléma akkor fordul elő, ha megpróbálja engedélyezni a funkciót egy meglévő Azure AD Connect-telepítéshez, amelyaz AD FS bejelentkezési metódusként konfigurálva van. A probléma oka az, hogy a varázsló nem kéri az ADFS-hitelesítő adatok megadását, mielőtt megpróbálna frissíteni a jogcímszabályokat az AD FS szolgáltatásban.
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect nem sikerült a telepítésnek, ha a helyszíni AD erdőn le tiltotta az NTLM. A probléma oka az, hogy az Azure AD Connect varázsló nem biztosít teljesen minősített hitelesítő adatokat a Kerberos-hitelesítéshez szükséges biztonsági környezetek létrehozásakor. Ez okozza a Kerberos-hitelesítés sikertelen, és az Azure AD Connect varázsló az NTLM használatával való visszalépés.

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
#### <a name="fixed-issues"></a>Hibák kijavítva:
* Kijavítottunk egy problémát, amely miatt nem hozható létre új szinkronizálási szabály, ha a Címke attribútum nincs feltöltve.
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect az NTLM használatával a helyszíni AD jelszó-szinkronizáláshoz való csatlakozáshoz kapcsolódott, annak ellenére, hogy a Kerberos elérhető. Ez a probléma akkor fordul elő, ha a helyszíni AD topológia egy vagy több tartományvezérlőt állított vissza egy biztonsági másolatból.
* Kijavítottuk azt a problémát, amely miatt a teljes szinkronizálási lépések szükségtelenül előfordulnak a frissítés után. Általában a frissítés után a teljes szinkronizálási lépések futtatására van szükség, ha a beépített szinkronizálási szabályok módosulnak. A problémát a változásészlelési logika hibája okozta, amely helytelenül észlelt változást, amikor újvonalas karakterekkel találkozott a szinkronizálási szabály kifejezésével. Az olvashatóság javítása érdekében a szövegsor új karakterei kerülnek a szinkronizálási szabály kifejezésbe.
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect-kiszolgáló nem működött megfelelően az automatikus frissítés után. Ez a probléma az 1.1.443.0-s (vagy korábbi) verziójú Azure AD Connect-kiszolgálókat érinti. A problémával kapcsolatos részletekért olvassa el a cikket, [amelyet az Azure AD Connect nem működik megfelelően az automatikus frissítés után.](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade)
* Kijavítottuk azt a hibát, amely miatt az automatikus frissítés 5 percenként újra próbálkozott, amikor hibák at észleltek. A javítás, automatikus frissítés újrapróbálkozások exponenciális back-off, ha hibák at tapasztal.
* Kijavítottuk azt a hibát, amely miatt a 611-es jelszó-szinkronizálási esemény helytelenül jelenik meg a Windows alkalmazás eseménynaplóiban **hiba**helyett **tájékoztatóként** . A 611-es esemény akkor jön létre, amikor a jelszó-szinkronizálás problémát tapasztal. 
* Kijavítottuk az Azure AD Connect varázsló egyik olyan problémája, amely lehetővé teszi a csoportvisszaírási szolgáltatás engedélyezését a csoportvisszaíráshoz szükséges szervezeti egység kiválasztása nélkül.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Hozzáadott egy hibaelhárítási feladatot az Azure AD Connect varázslóhoz a További feladatok csoportban. Az ügyfelek ezt a feladatot kihasználva elháríthatják a jelszó-szinkronizálással kapcsolatos problémákat, és összegyűjthetik az általános diagnosztikát. A jövőben a Hibaelhárítás feladat ki lesz terjesztve más címtár-szinkronizálással kapcsolatos problémákra is.
* Az Azure AD Connect mostantól támogatja a **Meglévő adatbázis használata**nevű új telepítési módot. Ez a telepítési mód lehetővé teszi az ügyfelek számára, hogy egy meglévő ADSync-adatbázist tartalmazó Azure AD Connectet telepítsenek. A szolgáltatásról további információt a [Meglévő adatbázis használata](how-to-connect-install-existing-database.md)című cikkben talál.
* A nagyobb biztonság érdekében az Azure AD Connect mostantól alapértelmezés szerint a TLS1.2 használatával csatlakozik az Azure AD-hez a címtár-szinkronizáláshoz. Korábban az alapértelmezett érték a TLS1.0 volt.
* Amikor az Azure AD Connect jelszó-szinkronizálási ügynök elindul, megpróbál csatlakozni az Azure AD jól ismert végpont jelszó szinkronizálása. Sikeres kapcsolat esetén a rendszer átirányítja egy régióspecifikus végpontra. Korábban a jelszó-szinkronizálási ügynök gyorsítótárazza a régióspecifikus végpontot, amíg újra nem indítja. Most az ügynök törli a gyorsítótárat, és újrapróbálkozik a jól ismert végponttal, ha csatlakozási problémát tapasztal a régióspecifikus végponttal. Ez a módosítás biztosítja, hogy a jelszó-szinkronizálás feladatátvételt egy másik régió-specifikus végpontra, ha a gyorsítótárazott régió-specifikus végpont már nem érhető el.
* Helyszíni AD-erdőből származó módosítások szinkronizálásához AD DS-fiók szükséges. Vagy (i) saját maga hozza létre az AD DS-fiókot, és adja meg hitelesítő adatait az Azure AD Connect nek, vagy (ii) adja meg egy vállalati rendszergazda hitelesítő adatait, és hagyja, hogy az Azure AD Connect hozza létre az AD DS-fiókot. Korábban (i) az azure AD Connect varázsló alapértelmezett beállítása. Most, (ii) az alapértelmezett beállítás.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* A Microsoft Azure Government Cloud és a Microsoft Cloud Germany támogatása hozzáadva.

### <a name="ad-fs-management"></a>AD FS kezelése
#### <a name="fixed-issues"></a>Hibák kijavítva:
* Az Initialize-ADSyncNGCKeysWriteBack parancsmag az AD prep PowerShell modulhelytelenül alkalmazta az ACL-eket az eszközregisztrációs tárolóra, és ezért csak a meglévő engedélyeket örökli.  Ez úgy lett frissítve, hogy a szinkronizálási szolgáltatásfiók megfelelő engedélyekkel rendelkezik.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Az AAD Connect Verify ADFS bejelentkezési feladat frissült, így nem csak az ADFS-ből történő tokenbeolvasással ellenőrzi a bejelentkezéseket a Microsoft Online-nal szemben.
* Új ADFS-farm AAD Connect használatával történő beállításakor az ADFS-hitelesítő adatokat kérő lapot áthelyezték, így az már azelőtt megtörténik, hogy a felhasználónak ADFS- és WAP-kiszolgálókat kelljen megadnia.  Ez lehetővé teszi, hogy az AAD Connect ellenőrizze, hogy a megadott fiók rendelkezik-e a megfelelő engedélyekkel.
* Az AAD Connect frissítése során a továbbiakban nem sikerül frissíteni, ha az ADFS AAD megbízhatósági kapcsolat frissítése sikertelen.  Ebben az esetben a felhasználó megfelelő figyelmeztető üzenetet kap, és az AAD Connect további feladaton keresztül alaphelyzetbe állítja a bizalmi kapcsolatot.

### <a name="seamless-single-sign-on"></a>Zökkenőmentes egyszeri bejelentkezés
#### <a name="fixed-issues"></a>Hibák kijavítva:
* Kijavítottunk egy hibát, amely miatt az Azure AD Connect varázsló hibát adott vissza, ha megpróbálja engedélyezni [a zökkenőmentes egyszeri bejelentkezést.](how-to-connect-sso.md) A hibaüzenet *a következő: "A Microsoft Azure AD Connect hitelesítési ügynök konfigurációja nem sikerült."* Ez a probléma azokat a meglévő ügyfeleket érinti, akik a [jelen cikkben](how-to-connect-pta-upgrade-preview-authentication-agents.md)ismertetett lépések alapján manuálisan frissítették a hitelesítési ügynökök előzetes verzióját az [áthaladási hitelesítéshez.](how-to-connect-sso.md)


## <a name="115610"></a>1.1.561.0
Állapot: július 23, 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Hiba kijavítva: 

* Kijavítottuk azt a problémát, amely miatt a "Out to AD - User ImmutableId" szinkronizálási szabályt el kellett távolítani:

  * A probléma akkor fordul elő, amikor az Azure AD Connect frissítése, vagy ha a feladat lehetőség *frissítésszinkronizálási konfiguráció* az Azure AD Connect varázsló az Azure AD Connect szinkronizálási konfiguráció jának frissítésére szolgál.
  
  * Ez a szinkronizálási szabály azon ügyfelekre vonatkozik, akik engedélyezték az [ms-DS-ConsistencyGuid forráshorgonyként funkciót.](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) Ez a funkció az 1.1.524.0-s verzióban és azt követően került bevezetésre. A szinkronizálási szabály eltávolításakor az Azure AD Connect már nem tudja felnagyítani a helyszíni AD ms-DS-ConsistencyGuid attribútumot az ObjectGuid attribútumértékkel. Ez nem akadályozza meg az új felhasználók azure AD-be való kiépítését.
  
  * A javítás biztosítja, hogy a szinkronizálási szabály a frissítés vagy a konfiguráció módosítása során már nem lesz eltávolítva, amíg a szolgáltatás engedélyezve van. A probléma által érintett meglévő ügyfelek esetében a javítás azt is biztosítja, hogy a szinkronizálási szabály az Azure AD Connect ezen verziójára való frissítés után visszakerüljön.

* Kijavítottunk egy problémát, amely miatt a beépített szinkronizálási szabályok 100-nál kisebb prioritási értékkel rendelkeznek:

  * A 0 és 99 között lévő elsőbbségi értékek általában az egyéni szinkronizálási szabályok számára vannak fenntartva. A frissítés során a használatra szóló szinkronizálási szabályok prioritási értékei frissülnek a szinkronizálási szabályok változásainak megfelelően. A probléma miatt a beépített szinkronizálási szabályok 100-nál kisebb prioritási értékkel rendelhetők hozzá.
  
  * A javítás megakadályozza a probléma előfordulását a frissítés során. A probléma által érintett meglévő ügyfelek elsőbbségi értékeit azonban nem állítja vissza. A jövőben külön javítást biztosítunk a helyreállításhoz.

* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect varázsló [tartomány- és szervezetiegység-szűrési képernyője](how-to-connect-install-custom.md#domain-and-ou-filtering) az *összes tartomány és szervezeti egység szinkronizálása* beállítást jeleníti meg a kijelölt módon, annak ellenére, hogy a szervezeti egység alapú szűrés engedélyezve van.

*   Kijavítottuk azt a hibát, amely miatt a Szinkronizálási szolgáltatáskezelő [Címtárpartíciók konfigurálása képernyője](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) hibát adott ki, ha a *Frissítés* gombra kattint. A hibaüzenet *a következő: "Hiba történt a tartományok frissítése közben: Nem sikerült a "System.Collections.ArrayList" típusú objektumot a következőbe adni, hogy írja be a következőt: "Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject" szöveg.* A hiba akkor fordul elő, ha új AD-tartomány tadott hozzá egy meglévő AD-erdőhöz, és a Frissítés gombbal próbálja frissíteni az Azure AD Connectet.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* [Az Automatikus frissítés szolgáltatás](how-to-connect-install-automatic-upgrade.md) ki bővült, hogy a következő konfigurációkkal rendelkező ügyfeleket támogassa:
  * Engedélyezte az eszköz visszaírási funkcióját.
  * Engedélyezte a csoportvisszaírási funkciót.
  * A telepítés nem Expressz vagy DirSync-frissítés.
  * Több mint 100 000 objektum van a metaverzumban.
  * Több erdőhöz csatlakozik. Az expressz beállítás csak egy erdőhöz kapcsolódik.
  * Az AD-összekötő fiók már nem az alapértelmezett MSOL_ fiók.
  * A kiszolgáló átmeneti üzemmódban van beállítva.
  * Engedélyezte a felhasználói visszaírási funkciót.
  
  >[!NOTE]
  >Az Automatikus frissítés funkció hatókörének bővítése hatással van az ügyfelekre az Azure AD Connect 1.1.105.0-s és azt követő buildjével. Ha nem szeretné, hogy az Azure AD Connect-kiszolgáló automatikusan frissüljön, a következő parancsmast kell futtatnia az Azure AD Connect-kiszolgálón: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Az automatikus frissítés engedélyezéséről és letiltásáról az [Azure AD Connect: Automatikus frissítés](how-to-connect-install-automatic-upgrade.md)című cikkben olvashat bővebben.

## <a name="115580"></a>1.1.558.0
Állapot: Nem fog megjelenni. A build változásait az 1.1.561.0 verzió tartalmazza.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Hiba kijavítva: 

* Kijavítottuk azt a problémát, amely miatt a ou-alapú szűrési konfiguráció frissítésekor a "Out to AD - User ImmutableId" szinkronizálási szabályt el kellett távolítani. Ez a szinkronizálási szabály szükséges az [ms-DS-ConsistencyGuid forráshorgonyként szolgáltatáshoz.](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)

* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect varázsló [tartomány- és szervezetiegység-szűrési képernyője](how-to-connect-install-custom.md#domain-and-ou-filtering) az *összes tartomány és szervezeti egység szinkronizálása* beállítást jeleníti meg a kijelölt módon, annak ellenére, hogy a szervezeti egység alapú szűrés engedélyezve van.

*   Kijavítottuk azt a hibát, amely miatt a Szinkronizálási szolgáltatáskezelő [Címtárpartíciók konfigurálása képernyője](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) hibát adott ki, ha a *Frissítés* gombra kattint. A hibaüzenet *a következő: "Hiba történt a tartományok frissítése közben: Nem sikerült a "System.Collections.ArrayList" típusú objektumot a következőbe adni, hogy írja be a következőt: "Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject" szöveg.* A hiba akkor fordul elő, ha új AD-tartomány tadott hozzá egy meglévő AD-erdőhöz, és a Frissítés gombbal próbálja frissíteni az Azure AD Connectet.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* [Az Automatikus frissítés szolgáltatás](how-to-connect-install-automatic-upgrade.md) ki bővült, hogy a következő konfigurációkkal rendelkező ügyfeleket támogassa:
  * Engedélyezte az eszköz visszaírási funkcióját.
  * Engedélyezte a csoportvisszaírási funkciót.
  * A telepítés nem Expressz vagy DirSync-frissítés.
  * Több mint 100 000 objektum van a metaverzumban.
  * Több erdőhöz csatlakozik. Az expressz beállítás csak egy erdőhöz kapcsolódik.
  * Az AD-összekötő fiók már nem az alapértelmezett MSOL_ fiók.
  * A kiszolgáló átmeneti üzemmódban van beállítva.
  * Engedélyezte a felhasználói visszaírási funkciót.
  
  >[!NOTE]
  >Az Automatikus frissítés funkció hatókörének bővítése hatással van az ügyfelekre az Azure AD Connect 1.1.105.0-s és azt követő buildjével. Ha nem szeretné, hogy az Azure AD Connect-kiszolgáló automatikusan frissüljön, a következő parancsmast kell futtatnia az Azure AD Connect-kiszolgálón: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Az automatikus frissítés engedélyezéséről és letiltásáról az [Azure AD Connect: Automatikus frissítés](how-to-connect-install-automatic-upgrade.md)című cikkben olvashat bővebben.

## <a name="115570"></a>1.1.557.0
Állapot: július 2017

>[!NOTE]
>Ez a build nem érhető el az ügyfelek számára az Azure AD Connect automatikus frissítési szolgáltatáson keresztül.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Hiba kijavítva: 
* Kijavítottuk az Initialize-ADSyncDomainJoinedComputerSync parancsmaggal kapcsolatos problémát, amely miatt a meglévő szolgáltatáscsatlakozási pont objektumon konfigurált ellenőrzött tartomány akkor is megváltozott, ha az még mindig érvényes tartomány. Ez a probléma akkor fordul elő, ha az Azure AD-bérlő több ellenőrzött tartománnyal rendelkezik, amelyek a szolgáltatás csatlakozási pontjának konfigurálására használhatók.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* A jelszó-visszaírás már előzetes verzióban érhető el a Microsoft Azure Government felhőjével és a Microsoft Cloud Germany szolgáltatással. Az Azure AD Connect különböző szolgáltatáspéldányok támogatásáról az [Azure AD Connect: A példányok speciális szempontjai](reference-connect-instances.md)című cikkben olvashat bővebben.

* Az Initialize-ADSyncDomainJoinedComputerSync parancsmag most már rendelkezik egy új választható paraméter nevű AzureADDomain. Ezzel a paraméterrel megadhatja, hogy melyik ellenőrzött tartományt kell használni a szolgáltatás csatlakozási pontjának konfigurálásához.

### <a name="pass-through-authentication"></a>Átmenő hitelesítés

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Az áthaladási hitelesítéshez szükséges ügynök neve *a Microsoft Azure AD alkalmazásproxy-összekötőről* a Microsoft Azure *AD Connect hitelesítési ügynökre*változott.

* Az átadó hitelesítés engedélyezése alapértelmezés szerint nem engedélyezi a jelszókivonat-szinkronizálást.


## <a name="115530"></a>1.1.553.0
Állapot: június 2017

> [!IMPORTANT]
> Ebben a buildben séma- és szinkronizálási szabálymódosítások történtek. Az Azure AD Connect szinkronizálási szolgáltatás elindítja a teljes importálási és teljes szinkronizálási lépéseket a frissítés után. A változások részleteit az alábbiakban ismertetjük. A teljes importálás és a teljes szinkronizálás lépéseinek a frissítés utáni ideiglenes elhalasztásához olvassa el a Teljes szinkronizálás lépéseit a Frissítés után a [Teljes szinkronizálás elhalasztása című](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)cikkben.
>
>

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása

#### <a name="known-issue"></a>Ismert probléma
* Van egy probléma, amely érinti az ügyfelek, akik az Azure AD Connect-szinkronizálás [iou-alapú szűrést](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) használó ügyfelek. Amikor az Azure AD Connect varázsló [tartomány- és szervezetiegység-szűrési lapjára](how-to-connect-install-custom.md#domain-and-ou-filtering) lép, a következő viselkedés rejlehet:
  * Ha a szervezeti egység alapú szűrés engedélyezve van, a **Kijelölt tartományok és szervezeti egységek szinkronizálása** beállítás van bejelölve.
  * Ellenkező esetben az **Összes tartomány és oUs szinkronizálása** beállítás be van jelölve.

A probléma az, hogy az **összes tartomány és ous szinkronizálása beállítás** mindig be van jelölve a varázsló futtatásakor.  Ez akkor is előfordul, ha a szervezeti egység alapú szűrés t korábban konfigurálták. Az AAD Connect konfigurációs módosításának mentése előtt győződjön meg arról, hogy a **kijelölt tartományok és oUs-ok szinkronizálása jelölőnégyzet be van jelölve,** és ellenőrizze, hogy a szinkronizálandó összes számítógép újra engedélyezve van-e. Ellenkező esetben a szervezeti egység alapú szűrés le lesz tiltva.

#### <a name="fixed-issues"></a>Hibák kijavítva:

* Kijavítottuk a Jelszó-visszaírással kapcsolatos problémát, amely lehetővé tette, hogy az Azure AD-rendszergazda alaphelyzetbe állítsa egy helyszíni AD kiemelt jogosultságú felhasználói fiók jelszavát. A probléma akkor fordul elő, amikor az Azure AD Connect megkapja a jelszó alaphelyzetbe állítása engedélyt a kiemelt fiók felett. Az Azure AD Connect ezen verziójában a probléma megoldása azáltal, hogy nem engedélyezi az Azure AD-rendszergazda számára egy tetszőleges helyszíni AD kiemelt jogosultságú felhasználói fiók jelszavának alaphelyzetbe állítását, kivéve, ha a rendszergazda a fiók tulajdonosa. További információt a [4033453 biztonsági tanácsadó ban talál.](https://technet.microsoft.com/library/security/4033453)

* Kijavítottuk az [ms-DS-ConsistencyGuid forráshorgonyként](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) szolgáltatással kapcsolatos problémát, amely nek az Azure AD Connect nem írja vissza a helyszíni AD ms-DS-ConsistencyGuid attribútumot. A probléma akkor fordul elő, ha több helyszíni AD-erdők hozzá az Azure AD Connect és a *felhasználói identitások léteznek több könyvtárak beállítás* van kiválasztva. Ilyen konfiguráció használata esetén az eredményül beszerzett szinkronizálási szabályok nem feltöltik a sourceAnchorBinary attribútumot a Metaverzumban. A sourceAnchorBinary attribútum az ms-DS-ConsistencyGuid attribútum forrásattribútuma. Ennek eredményeképpen az ms-DSConsistencyGuid attribútumvisszaírás nem történik meg. A probléma megoldásához a következő szinkronizálási szabályok frissültek annak érdekében, hogy a Metaverse sourceAnchorBinary attribútuma mindig ki legyen töltve:
  * Be az AD -InetOrgPerson AccountEnabled.xml
  * Be az AD -InetOrgPerson Common.xml
  * AD fájlból – User AccountEnabled.xml
  * AD -User Common.xml fájlból
  * Az AD -Felhasználói csatlakozás SOAInAAD.xml

* Korábban, még akkor is, ha az [ms-DS-consistencyGuid as Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funkció nincs engedélyezve, a "Out to AD – User ImmutableId" szinkronizálási szabály továbbra is hozzáadódik az Azure AD Connect. A hatás jóindulatú, és nem okoz az ms-DS-ConsistencyGuid attribútum visszaírását. A félreértések elkerülése érdekében a logika hozzáadva győződjön meg arról, hogy a szinkronizálási szabály csak akkor kerül hozzáadásra, ha a szolgáltatás engedélyezve van.

* Kijavítottuk azt a hibát, amely miatt a jelszókivonat-szinkronizálás a 611-es hibaesemény miatt meghiúsult. Ez a probléma azután fordul elő, hogy egy vagy több tartományvezérlőt eltávolítottak a helyszíni AD-ből. Minden jelszó-szinkronizálási ciklus végén a helyszíni AD által kiadott szinkronizálási cookie az eltávolított tartományvezérlők meghívási azonosítóit tartalmazza 0 USN (Update Sequence Number) értékkel. A Jelszó-szinkronizálási kezelő nem tudja megtartani a 0 USN-értéket tartalmazó szinkronizálási cookie-t, és a 611-es hibaesemény sikertelen. A következő szinkronizálási ciklus ban a Jelszó-szinkronizálási kezelő újrafelhasználja az utolsó megőrzött szinkronizálási cookie-t, amely nem tartalmaz 0 USN-értéket. Ez ugyanazt a jelszómódosítást eredményezi újraszinkronizálásra. Ezzel a javítással a Jelszó-szinkronizálási kezelő megfelelően megőrzi a szinkronizálási cookie-t.

* Korábban, még akkor is, ha az Automatikus frissítés le van tiltva a Set-ADSyncAutoUpgrade parancsmag használatával, az Automatikus frissítés imasszoló folyamat továbbra is rendszeresen ellenőrzi a frissítést, és a letöltött telepítőre támaszkodik a letiltás tiszteletben Ezzel a javítással az automatikus frissítési folyamat már nem ellenőrzi a frissítést rendszeresen. A javítás automatikusan érvénybe lép, ha az Azure AD Connect-verzió frissítéstelepítője egyszer kerül végrehajtásra.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* Korábban az [ms-DS-ConsistencyGuid as Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) szolgáltatás csak az új telepítések számára volt elérhető. Most már elérhető a meglévő központi telepítések. Pontosabban:
  * A szolgáltatás eléréséhez indítsa el az Azure AD Connect varázslót, és válassza a *Forráshorgony frissítése* lehetőséget.
  * Ez a beállítás csak az on-t a meglévő központi telepítések, amelyek objectGuid sourceAnchor attribútumként.
  * A beállítás konfigurálásakor a varázsló ellenőrzi az ms-DS-ConsistencyGuid attribútum állapotát a helyszíni Active Directoryban. Ha az attribútum nincs konfigurálva a címtár egyetlen felhasználói objektumán sem, a varázsló az ms-DS-ConsistencyGuid-ot használja sourceAnchor attribútumként. Ha az attribútum a címtár egy vagy több felhasználói objektumán van konfigurálva, a varázsló arra a következtetésre jut, hogy az attribútumot más alkalmazások használják, és nem alkalmas sourceAnchor attribútumként, és nem teszi lehetővé a Forráshorgony módosítását. Ha biztos abban, hogy az attribútumot nem használják a meglévő alkalmazások, a hiba letiltásával kapcsolatos információkért forduljon az ügyfélszolgálathoz.

* Az Azure AD Connect mostantól az Eszközobjektumok **userCertificate** attribútumára jellemző, és most antól megkeresi a tartományhoz csatlakozó eszközök csatlakoztatásához szükséges tanúsítványértékeket [a Windows 10 Azure AD-hez,](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) és kiszűri a többit, mielőtt szinkronizálna az Azure AD-vel. A viselkedés engedélyezéséhez a "Out to AAD - Device Join SOAInAD" in-box szinkronizálási szabály frissült.

* Az Azure AD Connect mostantól támogatja az Exchange Online **cloudPublicDelegates** attribútum visszaírását a helyszíni AD **publicDelegates** attribútumhoz. Ez lehetővé teszi azt a forgatókönyvet, amikor egy Exchange Online-postaláda SendOnBehalfTo jogosultságot kaphat a helyszíni Exchange-postaládával rendelkező felhasználóknak. A funkció támogatásához egy új, "Out to AD – User Exchange Hybrid PublicDelegates writeback" szinkronizálási szabály került hozzáadásra. Ez a szinkronizálási szabály csak akkor kerül az Azure AD Connectbe, ha az Exchange Hybrid szolgáltatás engedélyezve van.

* Az Azure AD Connect most már támogatja az **altRecipient** attribútum szinkronizálását az Azure AD-ből. A módosítás támogatásához a beépített szinkronizálási szabályok frissítése megtörtént, hogy tartalmazzák a szükséges attribútumfolyamatot:
  * Az AD -tól – Felhasználói csere
  * Kimenő AAD – User ExchangeOnline
  
* A Metaverse **felhőSOAExchMailbox** attribútuma azt jelzi, hogy egy adott felhasználó rendelkezik-e Exchange Online postaládával vagy sem. Definíciója frissült, hogy további Exchange Online RecipientDisplayTypes-eket is tartalmazzon, például a berendezéseket és a konferenciaterem postaládáit. A módosítás engedélyezéséhez a cloudSOAExchMailbox attribútum definíciója, amely az "In from AAD – User Exchange Hybrid" (Beépített a a aad-ból – User Exchange Hybrid' – in-in ) alatt található cloudSOAExchMailbox attribútum definíciója frissült:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... a következőkre:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Hozzáadva az X509Certificate2-kompatibilis függvények következő készletét a szinkronizálási szabálykifejezések létrehozásához a userCertificate attribútum tanúsítványértékeinek kezeléséhez:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm (CertKeyAlgorithm)|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert között|
    |CertFriendlyName (CertFriendlyName)|CertThumbprint|CertExtensionOidok|
    |Tanúsítványformátum|CertNotAfter után|CertPublicKeyOid (CertPublicKeyOid)|
    |CertSerialNumber|CertNotElőtte|CertPublicKeyParametersOid|
    |CertVersion (CertVersion)|CertSignatureAlgorithmOid|Válassza ezt:|
    |CertKeyAlgorithmParams|CertHashString|Ahol|
    |||With|

* A következő sémamódosítások at vezettek be, amelyek lehetővé teszik az ügyfelek számára, hogy egyéni szinkronizálási szabályokat hozzanak létre a csoportobjektumok sAMAccountName, domainNetBios és domainFQDN áramlásához, valamint a felhasználói objektumok megkülönböztető neve:

  * Az MV sémához a következő attribútumok lettek hozzáadva:
    * Csoport: Fióknév
    * Csoport: domainNetBios
    * Csoport: domainFQDN
    * Személy: distinguishedName

  * Az Azure AD Connector sémája a következő attribútumokat adta hozzá:
    * Csoport: OnPremisesSamAccountName
    * Csoport: NetBiosName
    * Csoport: DnsDomainName
    * Felhasználó: OnPremisesDistinguishedName

* Az ADSyncDomainJoinedComputerSync parancsmag-parancsfájl most már rendelkezik egy új választható paraméter nevű AzureEnvironment. A paraméter segítségével adhatja meg, hogy a megfelelő Azure Active Directory-bérlő melyik régióban található. Az érvényes értékek a következők:
  * AzureCloud (alapértelmezett)
  * AzureChinaCloud
  * AzureGermanyCloud
  * UsGovernment (Amerikai Egyesült Államok kormánya
 
* Frissítve a Szinkronizálási szabályszerkesztő, hogy a csatlakozás (a kiépítés helyett) a kapcsolattípus alapértelmezett értéke ként szolgáljon a szinkronizálási szabály létrehozása során.

### <a name="ad-fs-management"></a>AD FS-kezelés

#### <a name="issues-fixed"></a>Kijavított problémák

* A következő URL-címek az Azure AD által bevezetett új WS-összevonási végpontok, amelyek javítják a hitelesítési kimaradásokkal szembeni rugalmasságot, és hozzáadódnak a helyszíni AD FS válaszadó fél megbízhatósági konfigurációjához:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Kijavítottuk azt a problémát, amely miatt az AD FS helytelen jogcímértéket generált a IssuerID azonosítóhoz. A probléma akkor fordul elő, ha az Azure AD-bérlőben több ellenőrzött tartomány található, johndoe@us.contoso.comés a IssuerID jogcím létrehozásához használt userPrincipalName attribútum tartományutótagja legalább háromszintmély (például). A probléma a jogcímszabályok által használt regex frissítésével oldható meg.

#### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
* Korábban az Azure AD Connect által biztosított ADFS tanúsítványkezelési funkció csak az Azure AD Connecten keresztül felügyelt ADFS-farmokkal használható. Most már használhatja a funkciót az ADFS-farmok, amelyek nem kezelik az Azure AD Connect használatával.

## <a name="115240"></a>1.1.524.0
Megjelent: Május 2017

> [!IMPORTANT]
> Ebben a buildben séma- és szinkronizálási szabálymódosítások történtek. Az Azure AD Connect szinkronizálási szolgáltatás elindítja a teljes importálás és a teljes szinkronizálás lépéseit a frissítés után. A változások részleteit az alábbiakban ismertetjük.
>
>

**Javítottuk a problémákat:**

Az Azure AD Connect szinkronizálása

* Kijavítottuk azt a problémát, amely miatt az automatikus frissítés az Azure AD Connect kiszolgálón akkor is előfordult, ha az ügyfél letiltotta a szolgáltatást a Set-ADSyncAutoUpgrade parancsmag használatával. Ezzel a javítással a kiszolgálóautomatikus frissítési folyamat továbbra is rendszeres időközönként ellenőrzi a frissítést, de a letöltött telepítő tiszteletben tartja az automatikus frissítési konfigurációt.
* A DirSync helybeni frissítése során az Azure AD Connect létrehoz egy Azure AD-szolgáltatásfiókot, amelyet az Azure AD-összekötő az Azure AD-vel való szinkronizáláshoz használ. A fiók létrehozása után az Azure AD Connect hitelesíti magát az Azure AD-vel a fiók használatával. Néha a hitelesítés átmeneti problémák miatt sikertelen, ami a DirSync helyi frissítésének sikertelensítését okozza a következő hiba *miatt: "Hiba történt az AAD-szinkronizálási feladat végrehajtása: AADSTS50034: Az alkalmazásba való bejelentkezéshez a fiókot hozzá kell adni a xxx.onmicrosoft.com könyvtárhoz."* A DirSync-frissítés rugalmasságának javítása érdekében az Azure AD Connect most újrapróbálkozik a hitelesítési lépéssel.
* Hiba történt a 443-as buildtel, amely nek köszönhetően a DirSync helybeni frissítése sikeres lesz, de a címtár-szinkronizáláshoz szükséges profilok futtatása nem jön létre. A gyógyulási logika az Azure AD Connect buildje tartalmazza. Amikor az ügyfél erre a buildre frissít, az Azure AD Connect észleli a hiányzó futtatási profilokat, és létrehozza azokat.
* Kijavítottuk azt a hibát, amely miatt a jelszó-szinkronizálási folyamat nem indult el a 6900-as azonosítójú eseményazonosítóval, és *a "Egy elem ugyanazzal a kulccsal rendelkező elem már hozzá lett adva"* hibával. Ez a probléma akkor fordul elő, ha frissíti a szervezeti egység szűrési konfigurációját az AD konfigurációs partícióval. A probléma megoldásához a Jelszó-szinkronizálási folyamat mostantól csak az AD tartománypartíciók jelszómódosításait szinkronizálja. A nem tartományi partíciók, például a konfigurációs partíciók kimaradnak.
* Az Express telepítése során az Azure AD Connect létrehoz egy helyszíni AD DS-fiókot, amelyet az AD-összekötő a helyszíni AD-vel való kommunikációhoz használ. Korábban a fiók a felhasználói fiók-vezérlés attribútumon beállított PASSWD_NOTREQD jelzővel, a fiókban pedig véletlenszerű jelszóval jön létre. Most az Azure AD Connect explicit módon eltávolítja a PASSWD_NOTREQD jelzőt, miután a jelszó be van állítva a fiókban.
* Kijavítottuk azt a hibát, amely miatt a DirSync frissítése meghiúsult *a "holtpont történt az sql-kiszolgálón, amely alkalmazászárolást próbál beszerezni",* amikor a mailNickname attribútum megtalálható a helyszíni AD sémában, de nincs az AD felhasználói objektumosztályhoz kötve.
* Kijavítottuk azt a problémát, amely miatt az Eszköz-visszaírási szolgáltatás automatikusan le van tiltva, amikor egy rendszergazda frissíti az Azure AD Connect szinkronizálási konfigurációját az Azure AD Connect varázslóval. A problémát az okozza, hogy a varázsló előzetes ellenőrzést végez a meglévő eszközvisszaírási konfigurációra vonatkozóan a helyszíni AD-ben, és az ellenőrzés sikertelen lesz. A javítás az, hogy kihagyja az ellenőrzést, ha az eszköz visszaírása korábban már engedélyezve van.
* A szervezeti egység szűrésének konfigurálásához használhatja az Azure AD Connect varázslót vagy a Szinkronizálási szolgáltatáskezelőt. Korábban, ha az Azure AD Connect varázsló használatával konfigurálja a szervezeti egység szűrését, az ezt követően létrehozott új szervezeti egységek a címtár-szinkronizáláshoz is megjelennek. Ha nem szeretné, hogy új szervezeti egységek is szerepeljenek, a szervezeti egység szűrését a Szinkronizálási szolgáltatáskezelő vel kell konfigurálnia. Most már ugyanazt a viselkedést érheti el az Azure AD Connect varázslóval.
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect által igényelt tárolt eljárások a dbo-séma helyett a telepítési rendszergazda sémája alatt jönnek létre.
* Kijavítottuk azt a hibát, amely miatt az Azure AD által visszaadott TrackingId attribútum kimaradt az AAD Connect Server eseménynaplóiból. A probléma akkor fordul elő, ha az Azure AD Connect átirányítási üzenetet kap az Azure AD-től, és az Azure AD Connect nem tud csatlakozni a megadott végponthoz. A TrackingId-ot a támogatási mérnökök használják a szolgáltatás oldali naplókkal való korrelációra a hibaelhárítás során.
* Amikor az Azure AD Connect LargeObject-hibát kap az Azure AD-től, az Azure AD Connect létrehoz egy eseményt az EventID 6941-es azonosítóval, és a következő üzenet *jelenik meg: "A kiépített objektum túl nagy. Vágja le az attribútumértékek számát ezen az objektumon."* Ugyanakkor az Azure AD Connect félrevezető eseményt is generál az EventID 6900-as és *a "Microsoft.Online.Coexistence.ProvisionRetryException: Nem lehet kommunikálni a Windows Azure Active Directory szolgáltatással" üzenettel.* A félreértések minimalizálása érdekében az Azure AD Connect már nem hozza létre az utóbbi eseményt, amikor largeobject hiba érkezik.
* Kijavítottuk azt a problémát, amely miatt a Szinkronizálási szolgáltatáskezelő nem válaszol, amikor megpróbálta frissíteni az Általános LDAP-összekötő konfigurációját.

**Új funkciók/fejlesztések:**

Az Azure AD Connect szinkronizálása
* Szinkronizálási szabálymódosítások – A szinkronizálási szabályok következő módosításai lettek megvalósítva:
  * Frissítve az alapértelmezett szinkronizálási szabály, amely úgy lett beállítva, hogy ne exportálja a **userCertificate** és **userSMIMECertificate** attribútumokat, ha az attribútumok értéke meghaladja a 15 értéket.
  * Az **employeeID** és **az msExchBypassModerationLink** AD attribútumok mostantól szerepelnek az alapértelmezett szinkronizálási szabálykészletben.
  * Az AD **attribútumfénykép** eltávolításra került az alapértelmezett szinkronizálási szabálykészletből.
  * Hozzáadott **preferredDataLocation** a Metaverzum séma és AAD összekötő séma. Azok az ügyfelek, akik az Azure AD-ben szeretnék frissíteni az attribútumokat, egyéni szinkronizálási szabályokat valósíthatnak meg. 
  * **Felhasználótípus** hozzáadása a Metaverzum-sémához és az AAD-összekötő sémához. Azok az ügyfelek, akik az Azure AD-ben szeretnék frissíteni az attribútumokat, egyéni szinkronizálási szabályokat valósíthatnak meg.

* Az Azure AD Connect mostantól automatikusan engedélyezi a ConsistencyGuid attribútum használatát a helyszíni AD-objektumok forráshorgony attribútumaként. Továbbá az Azure AD Connect feltölti a ConsistencyGuid attribútumot az objectGuid attribútumértékkel, ha az üres. Ez a funkció csak új telepítés esetén alkalmazható. Ha többet szeretne megtudni erről a funkcióról, olvassa el az [Azure AD Connect: Tervezési fogalmak – Ms-DS-ConsistencyGuid használatával sourceAnchor néven](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)című cikket.
* Új hibaelhárítási parancsmag invoke-ADSyncDiagnostics bővült, hogy segítsen diagnosztizálni jelszó kivonat szinkronizálásával kapcsolatos problémák. A parancsmag használatáról a [Jelszókivonat-szinkronizálás elhárítása az Azure AD Connect szinkronizálásával című témakörben](tshoot-connect-password-hash-synchronization.md)olvashat.
* Az Azure AD Connect mostantól támogatja a levelezésre képes nyilvános mappa objektumok szinkronizálását a helyszíni AD-ről az Azure AD-re. A funkciót az Azure AD Connect varázsló val választható funkciók. A szolgáltatásról az [Office 365 Címtáralapú peremhálózati blokkolás a helyszíni postai nyilvános mappák támogatási csomagjai](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders)című cikkben olvashat bővebben.
* Az Azure AD Connect használatához AD DS-fiók szükséges a helyszíni AD-ből történő szinkronizáláshoz. Korábban, ha az Azure AD Connect expressz módban telepítette, megadhategy vállalati rendszergazdai fiók hitelesítő adatait, és az Azure AD Connect létrehozza a szükséges AD DS-fiókot. Azonban egy egyéni telepítés és erdők hozzáadása egy meglévő központi telepítés, akkor kellett megadnia az AD DS-fiók helyett. Most is lehetősége van arra, hogy egy vállalati rendszergazdai fiók hitelesítő adatait egy egyéni telepítés során, és hagyja, hogy az Azure AD Connect hozza létre az AD DS-fiók szükséges.
* Azure AD Connect now supports SQL AOA. Az Azure AD Connect telepítése előtt engedélyeznie kell az SQL AOA-t. A telepítés során az Azure AD Connect észleli, hogy a megadott SQL-példány engedélyezve van-e az SQL AOA-hoz. Ha az SQL AOA engedélyezve van, az Azure AD Connect tovább számol, hogy az SQL AOA szinkron replikáció vagy aszinkron replikáció használatára van-e konfigurálva. Az Elérhetőségi csoport figyelőjének beállításakor ajánlott a RegisterAllProvidersIP tulajdonságot 0-ra állítani. Ez a javaslat azért van, mert az Azure AD Connect jelenleg az SQL Natív ügyfél az SQL-hez való csatlakozáshoz, és az SQL natív ügyfél nem támogatja a MultiSubNetFailover tulajdonság használatát.
* Ha a LocalDB-t használja az Azure AD Connect-kiszolgáló adatbázisaként, és elérte a 10 GB-os méretkorlátot, a szinkronizálási szolgáltatás már nem indul el. Korábban shrinkdatabase műveletet kell végrehajtania a LocalDB-n, hogy elegendő adatbázis-helyet igényeljen a szinkronizálási szolgáltatás indításához. Ezt követően a Szinkronizálási szolgáltatáskezelő segítségével törölheti a futtatási előzményeket, hogy több adatbázis-területet igényeljen. Most a Start-ADSyncPurgeRunHistory parancsmag segítségével törölheti a helyi adatbázisból származó futtatási előzményadatokat a DB-terület visszaszerzéséhez. Továbbá ez a parancsmag támogatja az offline módot (a -offline paraméter megadásával), amely akkor használható, ha a szinkronizálási szolgáltatás nem fut. Megjegyzés: Az offline mód csak akkor használható, ha a szinkronizálási szolgáltatás nem fut, és a használt adatbázis LocalDB.
* A szükséges tárterület csökkentése érdekében az Azure AD Connect most tömöríti a szinkronizálási hibák részleteit, mielőtt a LocalDB/SQL-adatbázisokban tárolnák őket. Amikor az Azure AD Connect egy régebbi verziójáról frissít erre a verzióra, az Azure AD Connect egyszeri tömörítést hajt végre a meglévő szinkronizálási hibák részleteire.
* Korábban a szervezeti egység szűrési konfigurációjának frissítése után manuálisan kell futtatnia a Teljes importálás t, hogy a meglévő objektumok megfelelően szerepeljenek/kizárják a címtár-szinkronizálásból. Most az Azure AD Connect automatikusan elindítja a teljes importálást a következő szinkronizálási ciklusban. Továbbá a teljes importálás csak a frissítés által érintett AD-összekötőkre vonatkozik. Megjegyzés: ez a fejlesztés csak az Azure AD Connect varázslóval végrehajtott szervezeti egység-szűrési frissítésekre vonatkozik. Nem alkalmazható a szinkronizálási szolgáltatáskezelővel végzett szervezeti egység-szűrési frissítésre.
* Korábban a csoportalapú szűrés csak a Felhasználók, csoportok és névjegyobjektumokat támogatja. Mostantól a csoportalapú szűrés a számítógép-objektumokat is támogatja.
* Korábban törölheti az Összekötő tér adatait az Azure AD Connect szinkronizálási ütemezőjának letiltása nélkül. Most a Szinkronizálási szolgáltatáskezelő blokkolja az Összekötő tér adatainak törlését, ha azt észleli, hogy az ütemező engedélyezve van. Továbbá egy figyelmeztetést ad vissza, hogy tájékoztassa az ügyfeleket a lehetséges adatvesztésről, ha az összekötő téradatai törlődnek.
* Korábban le kell tiltania a PowerShell-átírást az Azure AD Connect varázslóhoz a megfelelő futtatáshoz. A probléma részben megoldódott. Engedélyezheti a PowerShell-átírást, ha az Azure AD Connect varázslót használja a szinkronizálási konfiguráció kezeléséhez. Le kell tiltania a PowerShell-átírást, ha az Azure AD Connect varázslót használja az ADFS-konfiguráció kezeléséhez.



## <a name="114860"></a>1.1.486.0
Megjelent: április 2017

**Javítottuk a problémákat:**
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect telepítése nem sikerült a Windows Server honosított verziójára.

## <a name="114840"></a>1.1.484.0
Megjelent: április 2017

**Ismert problémák:**

* Az Azure AD Connect ezen verziója nem lesz sikeres, ha az alábbi feltételek teljesülnek:
   1. A DirSync helyszíni frissítését vagy az Azure AD Connect friss telepítését hajtja végre.
   2. A Windows Server honosított verzióját használja, ahol a kiszolgálóbeépített rendszergazdai csoportjának neve nem "Rendszergazdák".
   3. Az Azure AD Connecttel telepített alapértelmezett SQL Server 2012 Express LocalDB-t használja ahelyett, hogy saját teljes SQL-t biztosítana.

**Javítottuk a problémákat:**

Az Azure AD Connect szinkronizálása
* Kijavítottuk azt a problémát, amely miatt a szinkronizálási ütemező kihagyja a teljes szinkronizálási lépést, ha egy vagy több összekötőből hiányzik az adott szinkronizálási lépés futtatási profilja. Például manuálisan hozzáadott egy összekötőt a Szinkronizálási szolgáltatáskezelő használatával anélkül, hogy delta importálási futtatási profilt hozna létre hozzá. Ez a javítás biztosítja, hogy a szinkronizálási ütemező továbbra is fut delta importálás más összekötők.
* Kijavítottuk azt a hibát, amely miatt a szinkronizálási szolgáltatás azonnal leállítja a futtatási profil feldolgozását, ha az valamelyik futtatási lépéssel kapcsolatos problémát tapasztal. Ez a javítás biztosítja, hogy a szinkronizálási szolgáltatás kihagyja a futó lépést, és folytatja a többit. Például rendelkezik egy különbözeti importálási futtatási profillal az AD-összekötőhöz, amely több futtatási lépéssel rendelkezik (minden helyszíni AD-tartományhoz egy). A szinkronizálási szolgáltatás akkor is futtatja a különbözeti importálást a többi AD-tartománnyal, ha valamelyikük hálózati kapcsolattal kapcsolatos problémákkal küzd.
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connector frissítése kimarad az automatikus frissítés során.
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect helytelenül állapította meg, hogy a kiszolgáló tartományvezérlő-e a telepítés során, ami viszont a DirSync-frissítés sikertelensítéséhez kötött.
* Kijavítottuk azt a problémát, amely miatt a DirSync helybeni frissítése nem hoz létre futtatási profilt az Azure AD-összekötőhöz.
* Kijavítottuk azt a problémát, amely miatt a Szinkronizálási szolgáltatáskezelő felhasználói felülete nem válaszol az Általános LDAP-összekötő konfigurálásakor.

AD FS-kezelés
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect varázsló meghibásodott, ha az AD FS elsődleges csomópontját áthelyezték egy másik kiszolgálóra.

Asztali sso
* Kijavítottuk az Azure AD Connect varázsló egyik hibáját, amely miatt a bejelentkezési képernyő nem teszi lehetővé az asztali egyszeri bejelentkezés szolgáltatás engedélyezését, ha az új telepítés során a Jelszó-szinkronizálást választotta bejelentkezési lehetőségként.

**Új funkciók/fejlesztések:**

Az Azure AD Connect szinkronizálása
* Az Azure AD Connect Sync mostantól támogatja a virtuális szolgáltatásfiók, a felügyelt szolgáltatásfiók és a csoportfelügyelt szolgáltatásfiók szolgáltatásfiók használatát. Ez csak az Azure AD Connect új telepítésére vonatkozik. Az Azure AD Connect telepítésekor:
    * Alapértelmezés szerint az Azure AD Connect varázsló létrehoz egy virtuális szolgáltatásfiókot, és azt használja a szolgáltatásfiókként.
    * Ha tartományvezérlőre telepít, az Azure AD Connect visszaáll a korábbi viselkedésre, ahol létrehoz egy tartományi felhasználói fiókot, és helyette azt használja szolgáltatási fiókként.
    * Az alapértelmezett viselkedést az alábbi beállítások egyikével bírálhatja felül:
      * Csoportkezelt szolgáltatásfiók
      * Felügyelt szolgáltatásfiók
      * Tartományi felhasználói fiók
      * Helyi felhasználói fiók
* Korábban, ha az Azure AD Connect összekötők frissítését vagy szinkronizálási szabálymódosításait tartalmazó új buildre frissít, az Azure AD Connect egy teljes szinkronizálási ciklust indít el. Most az Azure AD Connect szelektíven csak a frissítéssel rendelkező összekötők teljes importálása lépést indítja el, és a teljes szinkronizálási lépést csak a szinkronizálási szabály változásokkal rendelkező összekötők esetében.
* Korábban az exportálási törlési küszöbérték csak azokra az exportálásokra vonatkozik, amelyek a szinkronizálási ütemezőn keresztül aktiválódnak. Most a szolgáltatás ki van bővítve, hogy tartalmazza az ügyfél által a Szinkronizálási szolgáltatáskezelő használatával manuálisan aktivált exportálásokat.
* Az Azure AD-bérlőn van egy szolgáltatáskonfiguráció, amely jelzi, hogy a jelszó-szinkronizálási szolgáltatás engedélyezve van-e a bérlő számára, vagy sem. Korábban könnyen a szolgáltatás konfigurációját helytelenül konfigurálta az Azure AD Connect, ha egy aktív és egy átmeneti kiszolgáló. Most az Azure AD Connect megkísérli a szolgáltatás konfigurációját csak az aktív Azure AD Connect-kiszolgálóval konzisztensen tartani.
* Az Azure AD Connect varázsló most észleli és visszaküldi a figyelmeztetést, ha a helyszíni AD nem rendelkezik engedélyezett AD Lomtár.
* Korábban az Exportálás az Azure AD-be túllépi az időd, és sikertelen, ha a kötegben lévő objektumok együttes mérete meghaladja a bizonyos küszöbértéket. Most a szinkronizálási szolgáltatás újra megpróbálja újraküldeni az objektumokat külön, kisebb kötegekben, ha a probléma merül fel.
* A Szinkronizálási szolgáltatáskulcs-kezelő alkalmazás el lett távolítva a Windows Start menüjéből. A titkosítási kulcs kezelése továbbra is támogatott a miiskmu.exe parancssori felületén keresztül. A titkosítási kulcs kezeléséről az [Azure AD Connect Sync titkosítási kulcs ának elhagyása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key)című cikkben olvashat.
* Korábban, ha módosítja az Azure AD Connect szinkronizálási szolgáltatásfiók jelszavát, a szinkronizálási szolgáltatás nem lesz képes megfelelően elindítani, amíg el nem hagyta a titkosítási kulcsot, és újrainicializálta az Azure AD Connect szinkronizálási szolgáltatásfiók jelszavát. Most, ez a folyamat már nem szükséges.

Asztali sso

* Az Azure AD Connect varázslónak már nincs szüksége a 9090-es port nak a hálózaton való megnyitására az átmenő hitelesítés és az asztali egyszeri szolgáltató konfigurálásakor. Csak a 443-as port szükséges. 

## <a name="114430"></a>1.1.443.0
Megjelent: Március 2017

**Javítottuk a problémákat:**

Az Azure AD Connect szinkronizálása
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect varázsló meghibásodott, ha az Azure AD-összekötő megjelenítendő neve nem tartalmazza az Azure AD-bérlőhöz rendelt kezdeti onmicrosoft.com tartományt.
* Kijavítottuk azt a hibát, amely miatt az Azure AD Connect varázsló meghibásodott, miközben kapcsolatot létesített az SQL-adatbázissal, amikor a Szolgáltatásszinkronizálási fiók jelszava speciális karaktereket tartalmaz, például aposztrófot, kettőspontot és helyet.
* Kijavítottuk azt a hibát, amely miatt a "A lemezkép a lemezképtől eltérő horgonyt tartalmaz" hibaüzenet jelenik meg egy Azure AD Connect-kiszolgálón átmeneti módban, miután ideiglenesen kizárt egy helyszíni AD-objektumot a szinkronizálásból, majd újra felvette a szinkronizáláshoz.
* Kijavítottuk azt a hibát, amely miatt a "A DN által elhelyezett objektum egy fantom" hiba történt egy Azure AD Connect-kiszolgálón átmeneti módban, miután ideiglenesen kizárt egy helyszíni AD-objektumot a szinkronizálásból, majd újra felvette a szinkronizáláshoz.

AD FS-kezelés
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect varázsló nem frissíti az AD FS konfigurációját, és az alternatív bejelentkezési azonosító konfigurálása után beállítja a megfelelő jogcímeket a függő entitás megbízhatóságára.
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect varázsló nem tudta megfelelően kezelni azokat az AD FS-kiszolgálókat, amelyek szolgáltatásfiókjai a sAMAccountName formátum helyett userPrincipalName formátummal vannak konfigurálva.

Átmenő hitelesítés
* Kijavítottuk azt a problémát, amely miatt az Azure AD Connect varázsló meghibásodott, ha az Áthaladási hitelesítés van kiválasztva, de az összekötő regisztrációja sikertelen.
* Kijavítottuk azt a hibát, amely miatt az Azure AD Connect varázsló megkerülte a bejelentkezési módszer érvényesítési ellenőrzését, ha az asztali egyszeri bejelentkezés i. szolgáltatás engedélyezve van.

Új jelszó kérése
* Kijavítottuk azt a problémát, amely miatt az Azure AAD Connect kiszolgáló nem kísérelt meg újra csatlakozni, ha a kapcsolatot tűzfal vagy proxy ölte meg.

**Új funkciók/fejlesztések:**

Az Azure AD Connect szinkronizálása
* A Get-ADSyncScheduler parancsmag most egy SyncCycleInProgress nevű új logikai tulajdonságot ad vissza. Ha a visszaadott érték igaz, az azt jelenti, hogy egy ütemezett szinkronizálási ciklus van folyamatban.
* Az Azure AD Connect telepítési és telepítési naplóinak tárolására szolgáló célmappa átlett helyezve a %localappdata%\AADConnect mappából a %programdata%\AADConnect mappába a naplófájlok hozzáférhetőségének javítása érdekében.

AD FS-kezelés
* Az AD FS Farm TLS/SSL-tanúsítvány frissítésének támogatása hozzáadva.
* Az AD FS 2016 kezeléséhez nyújtott támogatás hozzáadva.
* Az AD FS telepítése során most már megadhatja a meglévő gMSA-t (csoportkezelt szolgáltatásfiók).
* Most már konfigurálhatja az SHA-256-ot az Azure AD függő entitás megbízhatóságának aláíráskivonat-algoritmusaként.

Új jelszó kérése
* Olyan fejlesztéseket vezetett be, amelyek lehetővé teszik a termék működését szigorúbb tűzfalszabályokkal rendelkező környezetekben.
* Továbbfejlesztett kapcsolatmegbízhatóság az Azure Service Busszolgáltatással.

## <a name="113800"></a>1.1.380.0
Megjelent: 2016 december

**Rögzített probléma:**

* Kijavítottuk azt a problémát, amely miatt az Active Directory összevonási szolgáltatások (AD FS) kibocsátási jogcímszabálya hiányzik a buildből.

>[!NOTE]
>Ez a build nem érhető el az ügyfelek számára az Azure AD Connect automatikus frissítési szolgáltatáson keresztül.

## <a name="113710"></a>1.1.371.0
Megjelent: 2016 december

**Ismert probléma:**

* Az AD FS problémajogcímszabálya hiányzik ebben a buildben. A problémaalapú jogcímszabály akkor szükséges, ha több tartományt egyesít az Azure Active Directoryval (Azure AD). Ha az Azure AD Connect használatával kezeli a helyszíni AD FS-telepítést, a buildre való frissítés eltávolítja a meglévő problémaalapú jogcímszabályt az AD FS-konfigurációból. A probléma megkerülése a probléma kiadására vonatkozó szabály hozzáadásával a telepítés/frissítés után. A problémaalapú jogcímszabály hozzáadásával kapcsolatos részletekért tekintse meg ezt a cikket az [Azure AD-vel való összeszerzés több tartomány támogatásáról.](how-to-connect-install-multiple-domains.md)

**Rögzített probléma:**

* Ha a 9090-es port nincs megnyitva a kimenő kapcsolathoz, az Azure AD Connect telepítése vagy frissítése sikertelen lesz.

>[!NOTE]
>Ez a build nem érhető el az ügyfelek számára az Azure AD Connect automatikus frissítési szolgáltatáson keresztül.

## <a name="113700"></a>1.1.370.0
Megjelent: 2016 december

**Ismert problémák:**

* Az AD FS problémajogcímszabálya hiányzik ebben a buildben. A problémaalapú jogcímszabály akkor szükséges, ha több tartományt egyesít az Azure AD-vel. Ha az Azure AD Connect használatával kezeli a helyszíni AD FS-telepítést, a buildre való frissítés eltávolítja a meglévő problémaalapú jogcímszabályt az AD FS-konfigurációból. A probléma megkerülése a probléma telepítését/frissítését követően a problémakiadásra vonatkozó szabály hozzáadásával. A problémaalapú jogcímszabály hozzáadásával kapcsolatos részletekért tekintse meg ezt a cikket az [Azure AD-vel való összeszerzés több tartomány támogatásáról.](how-to-connect-install-multiple-domains.md)
* A 9090-es portnak nyitottnak kell lennie a teljes telepítéshez.

**Új funkciók:**

* Átmenő hitelesítés (előzetes verzió).

>[!NOTE]
>Ez a build nem érhető el az ügyfelek számára az Azure AD Connect automatikus frissítési szolgáltatáson keresztül.

## <a name="113430"></a>1.1.343.0
Megjelent: November 2016

**Ismert probléma:**

* Az AD FS problémajogcímszabálya hiányzik ebben a buildben. A problémaalapú jogcímszabály akkor szükséges, ha több tartományt egyesít az Azure AD-vel. Ha az Azure AD Connect használatával kezeli a helyszíni AD FS-telepítést, a buildre való frissítés eltávolítja a meglévő problémaalapú jogcímszabályt az AD FS-konfigurációból. A probléma megkerülése a probléma telepítését/frissítését követően a problémakiadásra vonatkozó szabály hozzáadásával. A problémaalapú jogcímszabály hozzáadásával kapcsolatos részletekért tekintse meg ezt a cikket az [Azure AD-vel való összeszerzés több tartomány támogatásáról.](how-to-connect-install-multiple-domains.md)

**Javítottuk a problémákat:**

* Néha az Azure AD Connect telepítése sikertelen, mert nem tud létrehozni egy helyi szolgáltatásfiókot, amelynek jelszava megfelel a szervezet jelszóházirendje által meghatározott összetettségi szintnek.
* Kijavítottuk azt a problémát, amely miatt az illesztési szabályok at nem értékeli újra, amikor az összekötőtérben lévő objektum egyszerre hatókörön kívül esik az egyik illesztési szabályhoz, és hatókörön kívül esik egy másikszámára. Ez akkor fordulhat elő, ha két vagy több olyan csatlakozási szabálya van, amelyek csatlakozási feltételei kölcsönösen kizárják egymást.
* Kijavítottuk azt a problémát, amely miatt a bejövő szinkronizálási szabályok (az Azure AD-ből), amelyek nem tartalmaznak illesztési szabályokat, nem kerülnek feldolgozásra, ha alacsonyabb prioritási értékekkel rendelkeznek, mint az illesztési szabályokat tartalmazók.

**Fejlesztések:**

* Az Azure AD Connect windows Server 2016 Standard vagy újabb rendszeren történő telepítésének támogatása hozzáadva.
* Az SQL Server 2016 azure AD Connect távoli adatbázisként való használatának támogatása hozzáadva.

## <a name="112810"></a>1.1.281.0
Megjelent: Augusztus 2016

**Javítottuk a problémákat:**

* A szinkronizálási időköz módosítása csak a következő szinkronizálási ciklus befejezése után következik be.
* Az Azure AD Connect varázsló nem fogad el olyan Azure\_AD-fiókot, amelynek felhasználóneve aláhúzásjellel ( kezdődik).
* Az Azure AD Connect varázsló nem hitelesíti az Azure AD-fiókot, ha a fiók jelszó túl sok speciális karaktert tartalmaz. A következő hibaüzenet jelenik meg: "Nem lehet érvényesíteni a hitelesítő adatokat. Váratlan hiba történt." visszaadja a visszaadást.
* Az átmeneti kiszolgáló eltávolítása letiltja a jelszó-szinkronizálást az Azure AD-bérlőben, és a jelszó-szinkronizálás sikertelenaz aktív kiszolgálóval.
* A jelszó-szinkronizálás nem gyakori esetekben sikertelen, ha a felhasználó nem tárol jelszókivonatot.
* Ha az Azure AD Connect kiszolgáló engedélyezve van az átmeneti módban, a jelszó-visszaírás ideiglenesen nincs letiltva.
* Az Azure AD Connect varázsló nem jeleníti meg a tényleges jelszó-szinkronizálási és jelszó-visszaírási konfigurációt, ha a kiszolgáló átmeneti módban van. Mindig azt mutatja, hogy fogyatékkal élők.
* A jelszó-szinkronizálás és a jelszó-visszaírás konfigurációs módosításait az Azure AD Connect varázsló nem őrizi meg, ha a kiszolgáló átmeneti módban van.

**Fejlesztések:**

* Frissítette a Start-ADSyncSyncCycle parancsmagát annak jelzésére, hogy képes-e új szinkronizálási ciklust indítani vagy sem.
* Hozzáadva a Stop-ADSyncSyncCycle parancsmag a szinkronizálási ciklus és a művelet leállításához, amelyek jelenleg folyamatban vannak.
* A Stop-ADSyncScheduler parancsmag frissítése a szinkronizálási ciklus és a művelet leállításához, amely jelenleg folyamatban van.
* A [címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md) konfigurálásaaz Azure AD Connect varázsló, az Azure AD attribútum típusú "Teletex string" most már ki választható.

## <a name="111890"></a>1.1.189.0
Megjelent: június 2016

**Javítottunk problémákat és fejlesztéseket:**

* Az Azure AD Connect mostmár telepíthető egy FIPS-kompatibilis kiszolgálóra.
  * A jelszó-szinkronizálásról a [Jelszókivonat-szinkronizálás és](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips)a FIPS című témakörben van.
* Kijavítottuk azt a problémát, amely miatt az Active Directory-összekötőben nem oldható fel egy NetBIOS-név az FQDN-re.

## <a name="111800"></a>1.1.180.0
Megjelent: Május 2016

**Új funkciók:**

* Figyelmeztet, és segít a tartományok ellenőrzésében, ha nem tette meg az Azure AD Connect futtatása előtt.
* A [Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany)támogatása hozzáadva.
* A legújabb [Microsoft Azure Government felhőalapú](reference-connect-instances.md#microsoft-azure-government) infrastruktúra támogatása új URL-követelményekkel.

**Javítottunk problémákat és fejlesztéseket:**

* Szűrő vel bővült a Szinkronizálási szabályszerkesztő, hogy könnyen megtalálható legyen a szinkronizálási szabályok.
* Jobb teljesítmény az összekötőterület törlésekor.
* Kijavítottunk egy problémát, amikor ugyanazt az objektumot törölték és ugyanabban a futtatásban hozzáadták (törlésnek/hozzáadásnak nevezik).
* A letiltott szinkronizálási szabály már nem engedélyezi a beépített objektumokat és attribútumokat a frissítés vagy a címtárséma frissítése során.

## <a name="111300"></a>1.1.130.0
Megjelent: április 2016

**Új funkciók:**

* A címtárbővítmények többértékű [attribútumainak támogatása hozzáadva.](how-to-connect-sync-feature-directory-extensions.md)
* További konfigurációs változatok támogatása az [automatikus frissítéshez,](how-to-connect-install-automatic-upgrade.md) amely jogosultnak tekinthető a frissítésre.
* Hozzáadott néhány parancsmag [az egyéni ütemezőhöz](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Megjelent: Március 2016

**Javítottuk a problémákat:**

* Győződjön meg arról, hogy az Express telepítése nem használható Windows Server 2008 rendszeren (R2 előtti), mert a jelszószinkronizálás nem támogatott ezen az operációs rendszeren.
* A DirSync-ről egyéni szűrőkonfigurációval történő frissítés nem a várt módon működött.
* Amikor újabb kiadásra frissít, és nincs változás a konfigurációban, nem szabad teljes importálást/szinkronizálást ütemezni.

## <a name="111100"></a>1.1.110.0
Megjelent: február 2016

**Javítottuk a problémákat:**

* A korábbi kiadásokról történő frissítés nem működik, ha a telepítés nem az alapértelmezett C:\Program Files mappában található.
* Ha telepíti és törli **a szinkronizálási folyamat indítása** a telepítővarázsló végén, a telepítővarázsló második futtatása nem engedélyezi az ütemező számára.
* Az ütemező nem a várt módon működik azokon a kiszolgálókon, ahol az USA-en dátum/idő formátum ot nem használja. Azt is `Get-ADSyncScheduler` blokkolja, hogy visszatérjen a helyes idő.
* Ha az Azure AD Connect with AD FS egy korábbi kiadását telepítette bejelentkezési és frissítési lehetőségként, nem futtathatja újra a telepítővarázslót.

## <a name="111050"></a>1.1.105.0
Megjelent: február 2016

**Új funkciók:**

* [Automatikus frissítési](how-to-connect-install-automatic-upgrade.md) funkció az Expressz beállítások ügyfelei számára.
* Támogatja a globális rendszergazda segítségével Azure többtényezős hitelesítés és a kiemelt identitáskezelés a telepítő varázsló.
  * Engedélyeznie kell a proxy is, https://secure.aadcdn.microsoftonline-p.com hogy a forgalom, ha a többtényezős hitelesítés használata.
  * A többtényezős hitelesítés megfelelő működéséhez hozzá kell adnia https://secure.aadcdn.microsoftonline-p.com a megbízható helyek listájához.
* A kezdeti telepítés után engedélyezze a felhasználó bejelentkezési módszerének módosítását.
* [Tartomány- és szervezetiegység-szűrés](how-to-connect-install-custom.md#domain-and-ou-filtering) engedélyezése a telepítővarázslóban. Ez lehetővé teszi az olyan erdőkhöz való csatlakozást is, ahol nem minden tartomány érhető el.
* [Az ütemező](how-to-connect-sync-feature-scheduler.md) be van építve a szinkronizálási motorba.

**Az előzetes verziótól ga-ig előléptetett funkciók:**

* [Eszköz visszaírása](how-to-connect-device-writeback.md).
* [Könyvtárbővítmények](how-to-connect-sync-feature-directory-extensions.md).

**Új előzetes verziós funkciók:**

* Az új alapértelmezett szinkronizálási ciklusidőköz 30 perc. Az összes korábbi kiadásesetében három óra volt. Támogatást ad az [ütemező](how-to-connect-sync-feature-scheduler.md) viselkedésének módosításához.

**Javítottuk a problémákat:**

* Az ellenőrizze, hogy a DNS-tartományok lap nem mindig ismerte fel a tartományokat.
* Az AD FS konfigurálásakor a tartományfelügyeleti hitelesítő adatok megadására vonatkozó rákérdezés.
* A telepítővarázsló nem ismeri fel a helyszíni AD-fiókokat, ha a gyökértartománytól eltérő DNS-fával rendelkező tartományban találhatók.

## <a name="1091310"></a>1.0.9131.0
Megjelent: 2015 december

**Javítottuk a problémákat:**

* Előfordulhat, hogy a jelszószinkronizálás nem működik, ha módosítja a jelszavakat az Active Directory tartományi szolgáltatásokban, de akkor is működik, ha jelszót állít be.
* Ha rendelkezik egy proxykiszolgálóval, az Azure AD hitelesítése sikertelen lehet a telepítés során, vagy ha a frissítés megszakad a konfigurációs lapon.
* Az Azure AD Connect egy teljes SQL Server-példánysal történő frissítése sikertelen, ha ön nem SQL Server-rendszergazdai (SA) .
* Az Azure AD Connect egy távoli SQL Server kiszolgálóval való frissítése a "Nem érhető el az ADSync SQL-adatbázishoz" hibaüzenet jelenik meg.

## <a name="1091250"></a>1.0.9125.0
Megjelent: 2015.

**Új funkciók:**

* Újrakonfigurálhatja az AD FS-t az Azure AD megbízhatósági kapcsolatára.
* Frissítheti az Active Directory sémáját, és újragenerálhatja a szinkronizálási szabályokat.
* Letilthatja a szinkronizálási szabályt.
* A "Mérvadó Null" definiálható új konstansként egy szinkronizálási szabályban.

**Új előzetes verziós funkciók:**

* [Az Azure AD Connect állapota a szinkronizáláshoz.](how-to-connect-health-sync.md)
* Az [Azure AD tartományi szolgáltatások](../user-help/active-directory-passwords-update-your-own-password.md) jelszó-szinkronizálásának támogatása.

**Új támogatott forgatókönyv:**

* Több helyszíni Exchange-szervezetet támogat. További információt a [több Active Directory-erdővel rendelkező hibrid telepítések című témakörben talál.](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150))

**Javítottuk a problémákat:**

* Jelszó-szinkronizálási problémák:
  * A hatókörön kívülről a hatókörön kívülre áthelyezett objektumok jelszavát nem szinkronizálják. Ez magában foglalja a szervezeti egység és az attribútumszűrés t is.
  * A szinkronizálásba bevonandó új szervezeti egység kiválasztásához nincs szükség teljes jelszószinkronizálásra.
  * Ha egy letiltott felhasználó engedélyezve van, a jelszó nem szinkronizálódik.
  * A jelszó-újrapróbálkozási várólista végtelen, és a korábbi 5000 visszavont objektumkorlátot eltávolították.
* Nem lehet csatlakozni az Active Directoryhoz a Windows Server 2016 erdőszintű működési szintjén.
* Nem módosítható a csoportszűréshez használt csoport a kezdeti telepítés után.
* Már nem hoz létre új felhasználói profilt az Azure AD Connect kiszolgálón minden felhasználó számára, aki jelszó-módosítást végez jelszó-visszaírás engedélyezve.
* A hosszú egész értékek nem használhatók a szinkronizálási szabályok hatókörében.
* Az "eszközvisszaírás" jelölőnégyzet le tiltva marad, ha nem érhető el a tartományvezérlő.

## <a name="1086670"></a>1.0.8667.0
Megjelent: Augusztus 2015

**Új funkciók:**

* Az Azure AD Connect telepítővarázsló mostantól minden Windows Server-nyelvre honosítva van.
* Az Azure AD jelszókezelésének használatakor a fiók feloldása támogatással bővült.

**Javítottuk a problémákat:**

* Az Azure AD Connect telepítővarázsló összeomlik, ha egy másik felhasználó folytatja a telepítést, nem pedig az a személy, aki először indította el a telepítést.
* Ha az Azure AD Connect korábbi eltávolítása nem tudja eltávolítani az Azure AD Connect szinkronizálását, nem lehet újratelepíteni.
* Az Azure AD Connect nem telepíthető expressz telepítéssel, ha a felhasználó nem az erdő gyökértartományában van, vagy ha az Active Directory nem angol nyelvű verzióját használja.
* Ha az Active Directory felhasználói fiók teljes tartományneve nem oldható fel, a "Nem sikerült a séma véglegesítése" hibaüzenet jelenik meg.
* Ha az Active Directory-összekötőn használt fiók a varázslón kívül módosul, a varázsló sikertelen lesz a későbbi futtatások során.
* Az Azure AD Connect néha nem telepíthető tartományvezérlőre.
* Az "Átmeneti mód" nem engedélyezhető és nem tiltható le, ha bővítményattribútumokat adott hozzá.
* A jelszóvisszaírás egyes konfigurációkban sikertelen, mert az Active Directory-összekötőn rossz jelszó van.
* A DirSync nem frissíthető, ha megkülönböztető nevet (DN) használ az attribútumszűréshez.
* A túlzott CPU-használat a jelszó alaphelyzetbe állításakor.

**Eltávolított előnézeti funkciók:**

* Az előzetes verziójú [funkció, a Felhasználói visszaírás](how-to-connect-preview.md#user-writeback) ideiglenesen eltávolításra került az előzetes verzióban részt vevő ügyfeleink visszajelzései alapján. Ez lesz újra később, miután foglalkozott a megadott visszajelzést.

## <a name="1086410"></a>1.0.8641.0
Megjelent: június 2015

**Az Azure AD Connect kezdeti kiadása.**

Névmódosítása az Azure AD Sync-ről az Azure AD Connect re.

**Új funkciók:**

* [Expressz beállítások](how-to-connect-install-express.md) telepítése
* Konfigurálható [az AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* [Frissíthet a DirSync-ről](how-to-dirsync-upgrade-get-started.md)
* [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Bevezetett [átmeneti mód](how-to-connect-sync-staging-server.md)

**Új előzetes verziós funkciók:**

* [Felhasználói visszaírás](how-to-connect-preview.md#user-writeback)
* [Group writeback (Csoportvisszaíró)](how-to-connect-preview.md#group-writeback)
* [Eszközvisszaíró](how-to-connect-device-writeback.md)
* [Címtárbővítmények](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Megjelent: Május 2015

**Új követelmény:**

* Az Azure AD Sync most már telepíteni kell a .

**Javítottuk a problémákat:**

* Az Azure AD-ből történő jelszó-visszaírás sikertelen az Azure Service Bus kapcsolódási hibájával.

## <a name="104910413"></a>1.0.491.0413
Megjelent: április 2015

**Javítottunk problémákat és fejlesztéseket:**

* Az Active Directory-összekötő nem dolgozza fel megfelelően a törléseket, ha a lomtár engedélyezve van, és több tartomány van az erdőben.
* Az importálási műveletek teljesítménye javult az Azure Active Directory-összekötő.
* Ha egy csoport túllépte a tagsági korlátot (alapértelmezés szerint a korlát értéke 50 000 objektum), a csoport törlődött az Azure Active Directoryban. Az új viselkedés sel a csoport nem törlődik, hibaüzenet jelenik meg, és az új tagsági módosítások nem lesznek exportálva.
* Új objektum nem építhető ki, ha az összekötőtérben már szerepel egy ugyanazzal a DN-nel rendelkező szakaszos törlés.
* Egyes objektumok szinkronizálásra vannak megjelölve a különbözeti szinkronizálás során, még akkor is, ha az objektumon nincs módosítás.
* A jelszószinkronizálás kényszerítése eltávolítja az előnyben részesített tartományvezérlő-listát is.
* A CSExportAnalyzer-nek problémái vannak néhány objektumállapottal.

**Új funkciók:**

* Az illesztés most már csatlakozhat "ANY" objektumtípushoz az MV-ben.

## <a name="104850222"></a>1.0.485.0222
Megjelent: február 2015

**Fejlesztések:**

* Jobb importálási teljesítmény.

**Javítottuk a problémákat:**

* A Password Sync tiszteletben tartja az attribútumszűrés által használt cloudFiltered attribútumot. A szűrt objektumok már nincsenek a jelszó-szinkronizálás hatókörében.
* Ritka esetekben, amikor a topológia sok tartományvezérlővel volt meg, a jelszószinkronizálás nem működik.
* "Stopped-server" az Azure AD-összekötőről történő importáláskor, miután az Azure AD/Intune-ban engedélyezve van az eszközkezelés.
* Ha ugyanabból az erdőből több tartományból csatlakozik a külföldi rendszerbiztonsági tagokhoz (FSP) való csatlakozás nem egyértelmű illesztési hibát okoz.

## <a name="104751202"></a>1.0.475.1202
Megjelent: 2014 december

**Új funkciók:**

* A jelszó-szinkronizálás az attribútumalapú szűréssel mostantól támogatott. További információt a [Jelszó-szinkronizálás szűréssel című](how-to-connect-sync-configure-filtering.md)témakörben talál.
* Az ms-DS-ExternalDirectoryID attribútum visszaíródik az Active Directoryba. Ez a funkció támogatja az Office 365-alkalmazásokat. Az OAuth2 segítségével érhető el az online és a helyszíni postaládák egy hibrid exchange-telepítés.

**Javítottuk a frissítési problémákat:**

* A bejelentkezési asszisztens újabb verziója érhető el a kiszolgálón.
* Az Azure AD Sync telepítéséhez egyéni telepítési útvonalat használtak.
* Egy érvénytelen egyéni illesztési feltétel blokkolja a frissítést.

**Egyéb javítások:**

* Javítottuk az Office Pro Plus sablonjait.
* Kijavítottuk a kötőjellel kezdődő felhasználónevek által okozott telepítési problémákat.
* Javítva a sourceAnchor beállítás elvesztésével, amikor a telepítővarázslót másodszor is futtatta.
* Rögzített ETW nyomkövetés a jelszó szinkronizálásához.

## <a name="104701023"></a>1.0.470.1023
Megjelent: 2014 október

**Új funkciók:**

* Jelszó-szinkronizálás több helyszíni Active Directoryés az Azure AD között.
* Honosított telepítési felhasználói felület a Windows Server összes nyelvére.

**Frissítés AADSync 1.0 GA-ról**

Ha már telepítve van az Azure AD Sync, van egy további lépés, amelyet meg kell tennie, ha módosította a beépített szinkronizálási szabályok at. Az 1.0.470.1023 kiadásra való frissítés után a módosított szinkronizálási szabályok duplikálódnak. Minden módosított szinkronizálási szabályhoz tegye a következőket:

1. Keresse meg a módosított szinkronizálási szabályt, és jegyezze fel a módosításokat.
1. Törölje a szinkronizálási szabályt.
1. Keresse meg az Azure AD Sync által létrehozott új szinkronizálási szabályt, majd alkalmazza újra a módosításokat.

**Az Active Directory-fiók engedélyei**

Az Active Directory-fióknak további engedélyeket kell adni ahhoz, hogy olvasni tudja az Active Directory ból származó jelszókijelentéseket. A megadandó engedélyek neve "Címtármódosítások replikálása" és "A címtármódosítás iményének replikálása" néven szerepel. Mindkét engedély szükséges a jelszókiíráshoz.

## <a name="104190911"></a>1.0.419.0911
Megjelent: szeptember 2014

**Az Azure AD Sync kezdeti kiadása.**

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
