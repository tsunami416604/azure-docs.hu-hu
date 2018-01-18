---
title: "PowerShell-összekötő |} Microsoft Docs"
description: "Ez a cikk ismerteti a Microsoft Windows PowerShell-összekötő konfigurálásához."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6dba8e34-a874-4ff0-90bc-bd2b0a4199b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 2caf8dd8a657f116df0342893763829676602cd6
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="windows-powershell-connector-technical-reference"></a>Technikai útmutató a Windows PowerShell-összekötő
Ez a cikk ismerteti a Windows PowerShell-összekötő. A cikk vonatkozik a következő termékek:

* A Microsoft Identity Manager 2016 (MIM2016)
* A Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Kell használnia a 4.1.3671.0 gyorsjavítás vagy újabb [KB3092178](https://support.microsoft.com/kb/3092178).

MIM2016 és FIM2010R2, az összekötő rendelkezésre áll egy letölthető a [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>A PowerShell-összekötő áttekintése
A PowerShell-összekötő lehetővé teszi a szinkronizálási szolgáltatás integrálása külső rendszerek által biztosított Windows PowerShell-alapú API-k. Az összekötő hidat között a bővíthető kapcsolat Hívásalapú kezelőügynök képességeket biztosít, 2 (ECMA2) keretrendszer és a Windows PowerShell. ECMA keretében kapcsolatos további információkért tekintse meg a [bővíthető kapcsolat 2.2 felügyeleti ügynök hivatkozás](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Előfeltételek
Az összekötő használatához győződjön meg arról, hogy a szinkronizálási kiszolgálón a következő:

* Microsoft keretrendszer 4.5.2-es vagy újabb verzió
* A Windows PowerShell 2.0-s, 3.0-s vagy 4.0

Engedélyezi az összekötő a Windows PowerShell-parancsfájlok futtatásához a végrehajtási házirend a szinkronizálási szolgáltatás kiszolgálón kell konfigurálni. Ha a parancsfájlok digitális aláírással az összekötő fut, a végrehajtási házirend konfigurálása a következő parancs futtatásával:  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Új összekötő létrehozása
A szinkronizálási szolgáltatást a Windows PowerShell-összekötő létrehozása, egy Windows PowerShell-parancsfájlok hajtható végre a szinkronizálási szolgáltatás által kért lépéseket sorozatát kell megadnia. Attól függően, hogy az adatforrás csatlakozhat és a szükséges funkciókat meg kell valósítani a parancsfájlok függően változik. Ez a szakasz ismerteti az egyes a parancsfájlok, végrehajtható, és ha azok szükségesek.

A Windows PowerShell-összekötő a parancsfájlok a szinkronizálási szolgáltatás adatbázis belül mindegyikének tárolására szolgál. Bár a fájlrendszeren tárolt parancsfájlok futtatásához, célszerűbb minden parancsfájl közvetlenül az az összekötő-konfiguráció szövegtörzsét.

Egy PowerShell-összekötő létrehozásához az **szinkronizálási szolgáltatás** válasszon **kezelőügynök** és **létrehozása**. Válassza ki a **PowerShell (Microsoft)** összekötő.

![Összekötő létrehozása](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Kapcsolatok
Adja meg a konfigurációs paramétereket egy távoli rendszerre való kapcsolódáshoz. Ezek az értékek biztonságosan a Synchronization szolgáltatás által tárolt és elérhetővé tenni az a Windows PowerShell-parancsfájlok futtatásakor az összekötőt.

![Kapcsolatok](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

A következő csatlakozási paramétereket állíthatja be:

**Kapcsolatok**

| Paraméter | Alapértelmezett érték | Cél |
| --- | --- | --- |
| Kiszolgáló |<Blank> |Kiszolgáló neve, amely az összekötő csatlakoznia kell. |
| Tartomány |<Blank> |A hitelesítő adatok tárolására használható, az összekötő futtatásakor tartományán. |
| Felhasználó |<Blank> |A hitelesítő adatokat felhasználónév tárolására használható, az összekötő futtatásakor. |
| Jelszó |<Blank> |A hitelesítő adatok tárolására használható, az összekötő futtatásakor jelszavát. |
| Összekötő-fiók megszemélyesítése |False (Hamis) |Amikor igaz értékű, a szinkronizálási szolgáltatás fut a Windows PowerShell-parancsfájlok a megadott hitelesítő adatok környezetében. Ha lehetséges, javasoljuk, hogy a **$Credentials** paraméter átadása az egyes parancsfájl helyett a megszemélyesítési használatos. A beállítás használatához szükséges további engedélyeket további információkért lásd: [megszemélyesítéshez további konfigurációs](#additional-configuration-for-impersonation). |
| Felhasználói profil betöltése során megszemélyesítésekor |False (Hamis) |Arra utasítja az összekötő hitelesítő adatok a felhasználói profil betöltését a megszemélyesítés során. Ha a megszemélyesített felhasználóval központi profil, az összekötő nem töltődik be a központi profilban. Ez a paraméter használata szükséges további engedélyeket további információkért lásd: [megszemélyesítéshez további konfigurációs](#additional-configuration-for-impersonation). |
| Amikor megszemélyesítésekor bejelentkezési típusa |Nincs |Bejelentkezési típusa a megszemélyesítés során. További információkért lásd: a [dwLogonType] [ dw] dokumentációját. |
| Csak az aláírt parancsfájlok |False (Hamis) |Amennyiben az értéke igaz, a Windows PowerShell-összekötő azt ellenőrzi, hogy minden parancsprogram rendelkezik-e érvényes digitális aláírással. Ha értéke HAMIS, gondoskodjon arról, hogy a szinkronizálási szolgáltatás kiszolgáló Windows PowerShell végrehajtási házirendjét RemoteSigned vagy nem korlátozott. |

**Közös modul**  
Az összekötő lehetővé teszi egy megosztott Windows PowerShell-modul a konfiguráció tárolásához. Az összekötő egy parancsprogramot, ha a Windows PowerShell-modul ki kell olvasni a fájlrendszerhez, hogy minden parancsprogram importálhatók.

A jelszó-szinkronizálás, importálása és exportálása parancsfájlok a közös modul ki kell olvasni, az összekötő MAData mappába. A séma, az érvényesítési, a hierarchia és a partíció felderítési parancsfájlok a közös modul ki kell olvasni a % TEMP % mappában. Mindkét esetben a kibontott közös modul parancsfájl neve a közös parancsfájl modulnév beállításnak megfelelően.

A MAData mappából FIMPowerShellConnectorModule.psm1 nevű modul betöltéséhez használja a következő utasítást:`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

A % TEMP % mappában FIMPowerShellConnectorModule.psm1 hívása modul betöltéséhez használja a következő utasítást:`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**A paraméter érvényesítése**  
Az érvényesítési parancsfájlja egy nem kötelező Windows PowerShell-parancsfájl segítségével győződjön meg arról, hogy a rendszergazda által megadott összekötő-konfigurációs paraméterek érvényesek. Érvényesítéséhez server, a kapcsolat hitelesítő adatait, és a kapcsolódási paraméterek érvényesítési parancsfájlja közös használatra. Az érvényesítési parancsfájl neve után az alábbi lapokat, és a párbeszédpanelek módosítva lett:

* Kapcsolatok
* Globális paraméterek
* Partíció konfigurációját

Az érvényesítési parancsfájlja kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameterPage |[ConfigParameterPage][cpp] |A konfiguráció lapon vagy az ellenőrzési kérés kiváltó párbeszédpanel. |
| ConfigParameters |[KeyedCollection][keyk] [string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |

Az érvényesítési parancsfájlja egyetlen ParameterValidationResult objektumot kell visszaadnia, a folyamat.

**Séma felderítése**  
A séma felderítési parancsfájl megadása kötelező. Ez a parancsfájl az objektumtípusokat, attribútumokat és a szinkronizálási szolgáltatás által használt attribútum folyamata szabályok konfigurálásakor attribútum megkötések adja vissza. A séma felderítési parancsfájl összekötő létrehozása során fut, és feltölti az összekötő séma. A séma frissítése a művelet a Synchronization Service Managert is használják.

A séma felderítési parancsfájl kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk] [string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |

A parancsfájl kell visszaadnia egyetlen [séma] [ schema] a csővezeték-objektum. A séma objektum áll [SchemaType] [ schemaT] objektumtípusok képviselő objektumok (például: felhasználókat és csoportokat). A SchemaType objektum gyűjteményét tartalmazza [SchemaAttribute] [ schemaA] objektumok, amelyek megfelelnek az attribútumok (például: az Utónév, a Vezetéknév és a levelezési címe) típusú.

**További paraméterek**  
A szabványos konfigurációs beállítások mellett az összekötő a példányra vonatkozó további egyéni konfigurációs beállításokat adhat meg. Ezek a paraméterek adhatók meg, az összekötő, a partíció található, vagy futtatási lépés szintjeit, és a megfelelő Windows PowerShell-parancsfájl érhető el. Egyéni konfigurációs beállítások is tárolhatók a szinkronizálási szolgáltatás adatbázis egyszerű szöveges formátumban, vagy előfordulhat, hogy titkosítani. A szinkronizálási szolgáltatás automatikusan titkosítja, és visszafejtése biztonságos konfigurációs beállításokat, szükség esetén.

Egyéni konfigurációs beállítások megadásához külön minden paraméter neve vesszővel (,).

Egyéni konfigurációs beállításai egy parancsfájlból származó eléréséhez aláhúzásjellel nevét kell utótag ( \_ ) és a hatókör-paraméter (globális, partíciók vagy RunStep). Például a globális FileName paraméter elérésére használja következő kódrészletet:`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Funkciók
A képességek fülre a felügyeleti ügynök Designer viselkedését és az összekötő funkció határozza meg. Ezen a lapon megadott beállítások nem módosíthatók, az összekötő létrehozásakor. Ez a táblázat felsorolja a kapacitásbeállításai.

![Funkciók](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| Képesség | Leírás |
| --- | --- |
| [Megkülönböztető név stílus][dnstyle] |Azt jelzi, ha az összekötő támogatja az megkülönböztető nevét, és ha igen, milyen stílus. |
| [Exportálás típusa][exportT] |Meghatározza, hogy az exportálási parancsfájl számára megjelenített objektumok típusa. <li>AttributeReplace – például a többértékű attribútumhoz tartozó értékek teljes készletét, ha az attribútum.</li><li>AttributeUpdate – például csak többértékű attribútumra eltérések, ha az attribútum.</li><li>MultivaluedReferenceAttributeUpdate - értékek nem hivatkozásos többértékű attribútumok és többértékű hivatkozás attribútumok csak eltérések teljes készletét tartalmazza.</li><li>ObjectReplace – például egy objektum attribútumainak, ha bármely attribútum módosítások</li> |
| [Adatok normalizálási][DataNorm] |Arra utasítja a szinkronizálási szolgáltatás horgonyzási attribútumok optimalizálására, mielőtt parancsfájlok megadták. |
| [Objektum megerősítése][oconf] |A függőben lévő importálás viselkedés konfigurálja a szinkronizálási szolgáltatást. <li>Normál – alapértelmezett viselkedését, hogy meg kell erősíteni importálási keresztül minden exportált módosítások vár</li><li>NoDeleteConfirmation – a törölt objektum nincs nincs függőben lévő importálás jön létre.</li><li>NoAddAndDeleteConfirmation – objektum létrehozásakor vagy törölni, nincs nincs függőben lévő importálás jön létre.</li> |
| Megkülönböztető név használata alapjainak |Ha a megkülönböztető név stílus LDAP van beállítva, a kapcsolódási térbe horgonyzási attribútumát is megkülönböztető nevét. |
| Több összekötő párhuzamos műveletek |Ha be van jelölve, a Windows PowerShell több összekötő is futtatható egyidejűleg. |
| Partíciók |Ha be van jelölve, az összekötő támogatja több partíciót és partíció felderítése. |
| Hierarchia |Ha be van jelölve, az összekötő támogatja az egy LDAP-stílus hierarchikus struktúra. |
| Importálás engedélyezése |Ha be van jelölve, az összekötő importálja adatok importálása parancsfájlok segítségével. |
| Különbözeti importálás engedélyezése |Ha be van jelölve, az összekötő kérhetnek az eltérések az importálási parancsfájl. |
| Exportálás engedélyezése |Ha be van jelölve, az összekötő exportálja az adatokat exportálás parancsfájlok segítségével. |
| Teljes Exportálás engedélyezése |Ha be van jelölve, az Exportálás parancsfájlok támogatják, a teljes kapcsolódási térbe exportálása. Ez a beállítás használatához engedélyezze exportálása is ellenőrizni kell. |
| Nincs hivatkozási érték első exportálási fázis |Ha be van jelölve, hivatkozás attribútumok exportálása egy második exportálási fázisban. |
| Objektum átnevezése engedélyezése |Ha be van jelölve, a megkülönböztető nevét módosíthatja. |
| Törlés-hozzáadása, cseréje |Ha be van jelölve, törlése hozzáadási műveletek exportált egyetlen helyettesíti. |
| Jelszó műveletek engedélyezése |Ha be van jelölve, a jelszó-szinkronizálás parancsfájlok használata támogatott. |
| Az első fázisban exportálási jelszó engedélyezése |Ha be van jelölve, a jelszavak kiépítése során be exportálása az objektum létrehozásakor. |

### <a name="global-parameters"></a>Globális paraméterek
A felügyeleti ügynök Designer globális paraméterek lapon az összekötő által futtatott Windows PowerShell-parancsfájlok konfigurálását teszi lehetővé. A kapcsolat lapján definiált egyéni konfigurációs beállítások globális értékek is konfigurálhatja.

**Partíció felderítése**  
A partíció egy különálló névtér egy megosztott sémáján belül. Például az Active Directory minden tartományi partíció egy erdőn belül. Egy partíció a logikai csoportosításhoz importálás és exportálása a műveletek. Importálás és exportálás partícióval rendelkezik, mivel ebben a környezetben történik olyan környezetben, és minden műveletet. Partíciók elvileg határoz meg a hierarchiában, az LDAP-kiszolgálón. Megkülönböztető nevét, egy partíciót az importálási ellenőrzésére szolgál, hogy minden visszaadott objektum partíció hatókörén belül van-e. A partíció megkülönböztető neve is szolgál a kapcsolódási térbe a metaverzumba a kiépítés során egy objektumot lehet társítva exportálásakor a partíció meghatározásához.

A partíció felderítési parancsfájl kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |

A parancsfájl kell visszaadnia egy akár egyetlen [partíció] [ part] objektum vagy a folyamat partícióobjektumok [T] listája.

**Hierarchiafelderítés**  
A hierarchia felderítési parancsfájl csak akkor használja, ha a megkülönböztető név stílus funkció LDAP. A parancsfájl segítségével is keresse meg és kattintson a tárolók csoportja, amely akkor tekinthető, vagy kívül esik az importáláshoz és kiviteli művelet. A parancsfájl csak biztosítania kell, amely közvetlen a gyökércsomópont, a parancsfájl a megadott csomópontok listáját.

A hierarchia felderítési parancsfájl kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |
| ParentNode |[HierarchyNode][hn] |A gyökércsomópont a hierarchia, amely alatt a parancsfájl közvetlen gyermekei kell visszaadnia. |

A parancsfájl vagy kell visszaadnia egy vagy egyetlen HierarchyNode gyermekobjektum HierarchyNode gyermekobjektum [T] listája az adatcsatornához.

#### <a name="import"></a>Importálás
Importálási műveleteket támogató összekötők három parancsfájlok kell megvalósítania.

**A kezdő importálása**  
A begin importálási parancsfájl futtatása egy importálási futtatása lépés elején. Ezzel a lépéssel kapcsolatot létrehozni a forrásrendszerben, és az adatok importálása a csatlakoztatott rendszerből előtt előkészítő lépések.

A begin importálási parancsfájljának kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Értesíti a parancsfájl futtatása importálása (különbözeti vagy teljes), partíció, a hierarchia, a vízjel és a méretet a várt típusú. |
| Típusok |[Schema][schema] |A kapcsolódási térbe importált séma. |

A parancsfájl kell visszaadnia egyetlen [OpenImportConnectionResults] [ oicres] objektum a folyamat, például:`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Adatok importálása**  
Az importálási parancsfájl mindaddig, amíg a parancsfájl azt jelenti, hogy elfogytak az adatok importálása az összekötő neve. A Windows PowerShell-összekötő 9999 objektumok oldalméretet rendelkezik. A parancsfájl több mint 9999 az importálandó objektumok adja vissza, ha támogatniuk kell a lapozást. Az összekötő tesz elérhetővé, használható a tárolóba való vízjelet, hogy minden alkalommal, amikor az importálási parancsfájl egyéni tulajdonság neve, a parancsfájl folytatja, ahol abbamaradtak objektumok importálása.

Az importálási parancsfájl kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |
| GetImportEntriesRunStep |[ImportRunStep][irs] |A vízjel (CustomData) során használt lapozható importálja, és importálja a különbözeti tartalmazza. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Értesíti a parancsfájl futtatása importálása (különbözeti vagy teljes), partíció, a hierarchia, a vízjel és a méretet a várt típusú. |
| Típusok |[Schema][schema] |A kapcsolódási térbe importált séma. |

Az importálási parancsfájl kell írnia egy listát [[CSEntryChange][csec]] objektum az adatcsatornához. Ez a gyűjtemény CSEntryChange attribútumok, amelyek megfelelnek az egyes importált objektumhoz tevődik össze. A teljes importálás kísérletek során ez a gyűjtemény minden objektum összes attribútumának CSEntryChange objektum teljes készletét kell rendelkeznie. Során a különbözeti importálja a CSEntryChange objektum vagy tartalmaznia kell az attribútum szintű eltéréseit – az egyes objektumok importálásához, vagy a teljes változásai (a név felülírandó mód) objektumok megjelenítése.

**Záró importálása**  
Futtassa az importálás lezárásakor az End importálása parancsfájl futtatása. Ez a parancsfájl végre kell hajtania bármely Adattisztítási feladatok szükséges (például zárja be a rendszerekkel korábban létesített) és a hibák válaszolni.

Az end importálási parancsfájl kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Értesíti a parancsfájl futtatása importálása (különbözeti vagy teljes), partíció, a hierarchia, a vízjel és a méretet a várt típusú. |
| CloseImportConnectionRunStep |[CloseImportConnectionRunStep][cecrs] |A parancsfájl értesíti az importálás befejeződött okát. |

A parancsfájl kell visszaadnia egyetlen [CloseImportConnectionResults] [ cicres] objektum a folyamat, például:`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Exportálás
Azonos az import-architektúra az összekötő, exportálás támogató összekötők meg kell valósítania három parancsfájlok.

**Exportálás megkezdése**  
A begin exportálási parancsfájl futtatása egy Exportálás futtatási lépés elején. Ezzel a lépéssel a kapcsolatot a forrásrendszerben, és bármely előkészítő lépések elvégzésével a csatlakoztatott rendszer az adatok exportálása előtt.

A begin exportálási parancsfájl kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |A parancsfájl értesíti exportálás futtatási (különbözeti vagy teljes), partíció, a hierarchiában, és a méretet a várt típusú. |
| Típusok |[Schema][schema] |A kapcsolódási térbe exportált séma. |

A parancsfájl az adatcsatornához kell ad eredményül kimenetet.

**Adatok exportálása**  
A szinkronizálási szolgáltatás meghívja az adatok exportálása parancsfájl, ahányszor az összes függőben lévő exportálások feldolgozásához szükséges. Ha a kapcsolódási térbe további függőben lévő exportálások, mint az összekötő oldalméret, az exportálási parancsfájl metódus hívható többször és valószínűleg többször ugyanahhoz az objektumhoz.

Az exportálási parancsfájl kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |
| CSEntries |IList[CSEntryChange][csec] |Függőben lévő exportálásokról ebben a fázisban feldolgozandó objektumokként a kapcsolódási térbe listája. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |A parancsfájl értesíti exportálás futtatási (különbözeti vagy teljes), partíció, a hierarchiában, és a méretet a várt típusú. |
| Típusok |[Schema][schema] |A kapcsolódási térbe exportált séma. |

Az exportálási parancsfájl kell visszaadnia egy [PutExportEntriesResults] [ peeres] a csővezeték-objektum. Ez az objektum nem kell minden exportált összekötőhöz eredmény információkat tartalmazza, kivéve, ha hiba vagy módosítva lett a horgonyattribútum történik. Például az adatcsatornához vissza PutExportEntriesResults objektumot:`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Záró exportálása**  
Exportálás futtatási, a befejezési exportálása parancsfájl futtatásához lezárásakor. Ez a parancsfájl végre kell hajtania bármely Adattisztítási feladatok szükséges (például zárja be a rendszerekkel korábban létesített) és a hibák válaszolni.

Az end exportálási parancsfájl kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |A parancsfájl értesíti exportálás futtatási (különbözeti vagy teljes), partíció, a hierarchiában, és a méretet a várt típusú. |
| CloseExportConnectionRunStep |[CloseExportConnectionRunStep][cecrs] |A parancsfájl értesíti az exportálás befejeződött okát. |

A parancsfájl az adatcsatornához kell ad eredményül kimenetet.

#### <a name="password-synchronization"></a>A jelszó-szinkronizálás
A Windows PowerShell-összekötők kapcsolatos módosításokat/használható célként.

A jelszó parancsfájl kap az összekötő a következő paraméterekkel:

| Name (Név) | Adattípus | Leírás |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |Az összekötő-konfigurációs paraméterek tábla. |
| Hitelesítő adat |[PSCredential][pscred] |A kapcsolat lapon a rendszergazda által megadott hitelesítő adatokat tartalmazza. |
| Partíció |[Partition][part] |Alkalmazási címtárpartíció, amely a CSEntry. |
| CSEntry |[CSEntry][cse] |Összekötő terület bejegyzés az objektum, amely a jelszó módosítása vagy alaphelyzetbe állítása kapott. |
| OperationType |Karakterlánc |Azt jelzi, hogy a művelet a alaphelyzetbe állítása (**SetPassword**), vagy pedig módosultak (**ChangePassword**). |
| PasswordOptions |[PasswordOptions][pwdopt] |Jelző, adja meg a kívánt jelszavát alaphelyzetbe viselkedését. Ez a paraméter csak akkor érhető el, ha művelettípus **SetPassword**. |
| OldPassword |Karakterlánc |Az objektum régi jelszavát a jelszó-változtatásának feltöltve. Ez a paraméter csak akkor érhető el, ha művelettípus **ChangePassword**. |
| ÚjJelszó |Karakterlánc |Az objektum új jelszót, amelyet a parancsfájl-et kell beállítania feltöltve. |

A jelszó parancsfájl várhatóan nem a Windows PowerShell-feldolgozási folyamat vissza eredményt. Ha hiba lép fel a jelszó parancsfájl, a parancsfájl kell előidéznie a szinkronizálási szolgáltatás tájékoztatni a probléma a következő kivételekkel egyikét:

* [PasswordPolicyViolationException] [ pwdex1] – vált ki, ha a jelszó nem felel meg a jelszó-házirend a csatlakoztatott rendszerben.
* [PasswordIllFormedException] [ pwdex2] – vált ki, ha a jelszó érvénytelen a csatlakoztatott rendszer.
* [PasswordExtension] [ pwdex3] – a jelszó parancsfájlban más hibák lépett fel.

## <a name="sample-connectors"></a>A minta-összekötők
A rendelkezésre álló minta összekötők teljes áttekintése, lásd: [Windows PowerShell Connector összekötő mintavételt][samp].

## <a name="other-notes"></a>Egyéb megjegyzések
### <a name="additional-configuration-for-impersonation"></a>További konfigurációs a megszemélyesítéshez
Adja meg, amely a felhasználó nevében a következő engedélyeket a Synchronization Service-kiszolgálón:

Olvasási hozzáférés a következő beállításkulcsokat:

* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Software\Microsoft\PowerShell
* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Environment

Annak megállapításához, a biztonsági azonosítók (SID) a Synchronization Service szolgáltatás fiókjának, futtassa a következő PowerShell-parancsokat:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Olvasási hozzáférés a következő fájl rendszer mappák:

* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\Extensions
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\MaData\\{ConnectorName}

{Következőnek} helyőrző helyettesítse be a Windows PowerShell-összekötő nevét.

## <a name="troubleshooting"></a>Hibaelhárítás
* Az összekötő hibaelhárítása naplózásának engedélyezése a további információkért lásd: a [hogyan ETW-nyomkövetés engedélyezése a csatlakozók](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
