---
title: "Generate and transfer HSM által védett kulcsok Azure Key vault és hogyan |} Microsoft Docs"
description: "Ez a cikk használatával alakítsa ki tervezése, létrehozása és a saját HSM által védett kulcsok használata az Azure Key Vault majd át. Más néven BYOK vagy a saját kulcs."
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: ambapat
ms.openlocfilehash: 8c56c7a5e48d956353d965337e0ccd1a7c5131b3
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Generate and transfer HSM által védett és hogyan kulcsok Azure Key vault
## <a name="introduction"></a>Bevezetés
A nagyobb az Azure Key Vault használatakor importálhat vagy kulcsok létrehozása a hardveres biztonsági modulokkal (HSM), amely sosem hagyják el a HSM határait. Ebben a forgatókönyvben gyakran nevezik *a saját kulcs*, vagy BYOK. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Az Azure Key Vault Ön kulcsainak védelmét Thales nShield hardveres biztonsági modulok család használja.

Ez a témakör az információk használatával alakítsa ki tervezése, létrehozása és a saját HSM által védett kulcsok használata az Azure Key Vault majd át.

Ez a funkció nem érhető el Azure Kínában.

> [!NOTE]
> Az Azure Key Vault kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](key-vault-whatis.md)  
>
> Egy alapszintű bemutató, amely tartalmazza a HSM által védett kulcsok kulcstároló létrehozása, lásd: [Ismerkedés az Azure Key Vault](key-vault-get-started.md).
>
>

További információ létrehozása és átvitele egy HSM által védett kulcsot az interneten keresztül:

* A kulcs generálása egy kapcsolat nélküli munkaállomást, ami csökkenti a támadási felületet.
* A kulcs titkosított rendelkező egy kulcs kulcscserekulcs (KEK), amely átvitelig titkosítva marad, amíg az Azure Key Vault HSM kerül át. A kulcs csak a titkosított verziója hagyja el az eredeti munkaállomást.
* Az eszközkészlet a bérlői kulcs, amely az Azure Key Vault biztonsági világ kötve van a kulcs tulajdonságainak megadása. Ezt követően az Azure Key Vault HSM kap, és fejti vissza a kulcsot, csak ezek a HSM használható. A kulcs nem exportálható. Ezt a kötést a Thales HSM-eket.
* A kulcscserekulcs (KEK), hogy a kulcs titkosítására szolgál az Azure Key Vault HSM belül jön létre, és nem exportálható. A HSM-EK biztosítják, hogy az nem titkosítatlan verziója a HSM-EK kívül KEK lehet. Emellett az eszközkészlet által kiadott igazolást tartalmaz a Thales, hogy a KEK nem exportálható, és egy eredeti HSM-en, a Thales által gyártott rendszer belül jött létre.
* Az eszközkészlet igazolást tartalmaz arról, hogy az Azure Key Vault biztonsági világ szintén létrejött egy eredeti Thales által gyártott HSM Thales is. Az igazolás így Ön megbizonyosodhat arról, hogy a Microsoft eredeti hardvereket használ.
* A Microsoft külön kek használ, és biztonsági világot minden egyes földrajzi régióban. Ez az elkülönítés biztosítja, hogy használható-e a kulcsot csak az adatközpontokban a régióban, ahol titkosítva lett. Például egy Európai ügyfél kulcs nem használható az Észak-amerikai vagy ázsiai adatközpontokban.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>További információ a Thales HSM-ekről és a Microsoft-szolgáltatások
A Thales e-Security vezető globális szolgáltató, amely adattitkosítási és internetes biztonsági megoldásokat nyújt a pénzügyi szolgáltatásokat, a csúcstechnológiai, a gyártási, a kormányzati és a technológiai szektorok részére. Egy 40 éves tapasztalattal bíró védelmének vállalati és kormányzati információk Thales megoldásait négy használja a öt legnagyobb energetikai és űrtechnikai vállalatok. A megoldások 22 NATO országok is használják, és biztonságos világszerte a pénzügyi tranzakciók több mint 80 %-át.

A Microsoft és a Thales együttműködése argentin állapotának javítható a HSM-EK közvetlenül az rendelkezik. Az ilyen fejlesztések lehetővé teszik a kulcsok szabályozható lemondana üzemeltetett szolgáltatások tipikus előnyeit. Pontosabban a fejlesztéseknek köszönhetően a Microsoft felügyelje a HSM-eket, így nem kell. Felhő alapú szolgáltatásként az Azure Key Vault rendkívül gyorsan igazodik a szervezete használati igényeiben jelentkező teljesítéséhez. Ugyanakkor az Ön kulcsának védelmét a Microsoft HSM-jei: az Ön kezében a kulcs életciklusa fölötti, mert a kulcs létrehozása, és vigye át a Microsoft HSM-eket.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Az Azure Key Vault a saját kulcs (használatának BYOK) megvalósítása kapcsolása
Használja a következő információkat és eljárásokat, ha saját HSM által védett kulcs létrehozása és átvitele az Azure Key Vault fogja – a saját kulcs (használatának BYOK) forgatókönyv.

## <a name="prerequisites-for-byok"></a>A BYOK előfeltételei
Lásd az alábbi táblázatban az Azure Key Vault teheti a saját kulcs (használatának BYOK) előfeltételeit listáját.

| Követelmény | További információ |
| --- | --- |
| Azure-előfizetéssel |Egy Azure Key Vault létrehozásához Azure-előfizetés szükséges: [regisztráljon az ingyenes próbaverzióhoz](https://azure.microsoft.com/pricing/free-trial/) |
| A Key Vault prémium szintű szolgáltatási rétegben HSM által védett kulcsokat támogató |Az Azure Key Vault szolgáltatási szinteket és képességeire vonatkozó további információkért lásd: a [Azure Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/) webhelyet. |
| A Thales HSM, intelligens kártyák és a szoftvert |A Thales hardveres biztonsági modul és a Thales HSM-ekről alapvető ismeretekre hozzáféréssel kell rendelkeznie. Lásd: [Thales hardveres biztonsági modul](https://www.thales-esecurity.com/msrms/buy) listája kompatibilis modellek vagy HSM vásárlásához, ha még nem rendelkezik ilyennel. |
| A következő hardverre és szoftverekre:<ol><li>Egy kapcsolat nélküli x64 munkaállomás, amelyen a Windows 7 és a Thales nShield szoftver legalább egy minimális Windows operációs rendszer verziója 11.50.<br/><br/>Ha ez a munkaállomás Windows 7 rendszeren fut kell [Microsoft .NET-keretrendszer 4.5-ös verziójának telepítése](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Egy munkaállomást, amely kapcsolódik az internethez, és a Windows 7 minimális Windows operációs rendszert és [Azure PowerShell](/powershell/azure/overview) **minimális verziója 1.1.0-ás** telepítve.</li><li>Egy USB-meghajtó vagy más hordozható tárolóeszköz, amelyen legalább 16 MB szabad tárhely.</li></ol> |Biztonsági okokból azt javasoljuk, hogy az első munkaállomásra nincs csatlakoztatva a hálózathoz. Azonban ez a javaslat nem szoftveres követelmény.<br/><br/>Vegye figyelembe, hogy az alábbi utasításokat, az ezen a munkaállomáson a neve a kapcsolat nélküli munkaállomáson.</p></blockquote><br/>Emellett ha a bérlői kulcs éles hálózati környezetben, azt javasoljuk, hogy az eszközkészlet letöltésére és a bérlőkulcs feltöltésére egy második, különálló munkaállomást használjon. De tesztelési célokra is használhatja a munkaállomást az elsőt.<br/><br/>Vegye figyelembe, hogy az alábbi utasításokat, az erre a második munkaállomásra nevezzük az internetre kapcsolódó munkaállomásra.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Hozza létre, és a kulcs átvitele az Azure Key Vault HSM-be
A következő öt lépése hozhatnak létre és a kulcs átvitele az Azure Key Vault hardveres biztonsági MODULT fog használni:

* [1. lépés: Az internetre kapcsolódó munkaállomás előkészítése](#step-1-prepare-your-internet-connected-workstation)
* [2. lépés: A kapcsolat nélküli munkaállomás előkészítése](#step-2-prepare-your-disconnected-workstation)
* [3. lépés: A kulcs létrehozása](#step-3-generate-your-key)
* [4. lépés: A kulcs Áthelyezés előkészítése](#step-4-prepare-your-key-for-transfer)
* [5. lépés: A kulcs átvitele az Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>1. lépés: Az internetre kapcsolódó munkaállomás előkészítése
Az első lépéshez hajtsa végre az alábbi eljárások a az internethez csatlakoztatott munkaállomáson.

### <a name="step-11-install-azure-powershell"></a>1.1. lépés: Az Azure PowerShell telepítése
Az internetre kapcsolódó munkaállomáson töltse le és telepítse az Azure PowerShell modul, amely tartalmazza a parancsmagok az Azure Key Vault kezeléséhez. Ehhez a 0.8.13 verzióját.

A telepítési utasításokért lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>1.2. lépés: Az Azure előfizetés-azonosító lekérése
Indítson el egy Azure PowerShell-munkamenetet, és az Azure-fiókjával jelentkezzen be a következő paranccsal:

        Add-AzureAccount
Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Ezt követően a [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) parancs:

        Get-AzureSubscription
A kimenetben keresse meg az Azure Key Vault használandó előfizetés azonosítója. Később szüksége lesz az előfizetés-azonosító.

Ne zárja be az Azure PowerShell-ablakot.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>1.3. lépés: Az Azure Key Vault a BYOK eszközkészlet letöltése
Keresse fel a Microsoft Download Center és [az Azure Key Vault BYOK eszközkészlet letöltésére](http://www.microsoft.com/download/details.aspx?id=45345) a földrajzi régióban vagy Azure példányát. Az alábbi információk segítségével határozza meg a csomag nevét, letöltése és a megfelelő SHA-256 csomag kivonata:

- - -
**Egyesült Államok:**

KeyVault-BYOK-eszközök – Egyesült States.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**Európa:**

KeyVault-BYOK-eszközök – Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**Ázsia:**

KeyVault-BYOK-eszközök – AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

- - -
**Latin-Amerika:**

KeyVault-BYOK-eszközök – LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

- - -
**Japán:**

KeyVault-BYOK-eszközök – Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

- - -
**Korea:**

KeyVault-BYOK-eszközök – Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

- - -
**Ausztrália:**

KeyVault-BYOK-eszközök – Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Az Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-eszközök – USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**Egyesült Államok kormánya DOD:**

KeyVault-BYOK-eszközök – USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

- - -
**Kanada:**

KeyVault-BYOK-eszközök – Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

- - -
**Németország:**

KeyVault-BYOK-eszközök – Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

- - -
**India:**

KeyVault-BYOK-eszközök – India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

- - -
**Egyesült Királyság:**

KeyVault-BYOK-eszközök – UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

A letöltött BYOK eszközkészlet, az Azure PowerShell-munkamenetben integritásának ellenőrzéséhez használja a [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) parancsmag.

    Get-FileHash KeyVault-BYOK-Tools-*.zip

Az eszközkészlet a következőket tartalmazza:

* Egy kulcscserekulcs (KEK) csomag, amelynek a neve a **BYOK-KEK - pkg-.**
* Egy Biztonságivilág-csomag, amelynek a neve a **BYOK-SecurityWorld - pkg-.**
* Nevű python-parancsfájl **verifykeypackage.py.**
* Egy parancssori végrehajtható fájl **KeyTransferRemote.exe** és kapcsolódó dll-fájlok.
* A Visual C++ terjeszthető csomag **vcredist_x64.exe.**

Másolja a csomagot egy USB-meghajtó vagy más hordozható tárolóeszközre.

## <a name="step-2-prepare-your-disconnected-workstation"></a>2. lépés: A kapcsolat nélküli munkaállomás előkészítése
A második lépés hajtsa végre az alábbi eljárások a munkaállomáson, amely nem kapcsolódik a hálózathoz (vagy az interneten, vagy a belső hálózaton).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>2.1. lépés: Felkészülés a kapcsolat nélküli munkaállomáson Thales HSM-mel
Telepítse az nCipher (Thales) támogatószoftvert egy Windows-számítógépen, és majd csatoljon egy Thales HSM arra a számítógépre.

Gondoskodjon arról, hogy a Thales-eszközök az elérési úthoz (**%nfast_home%\bin**). Írja be például a következőt:

        set PATH=%PATH%;"%nfast_home%\bin"

További információkért lásd: a Thales HSM-en mellékelt felhasználói útmutatót.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>2.2. lépés: A BYOK eszközkészlet telepítése a kapcsolat nélküli munkaállomáson
Másolja át a BYOK eszközkészletcsomagot az USB-meghajtóra vagy egyéb hordozható tárolóeszköz, és tegye a következőket:

1. Csomagolja ki a fájlokat a letöltött csomag egy tetszőleges mappába.
2. Ebből a könyvtárból futtassa a vcredist_x64.exe.
3. Kövesse az utasításokat a telepítése a Visual C++ futtatókörnyezeti összetevők Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>3. lépés: A kulcs létrehozása
A harmadik lépése hajtsa végre az alábbi eljárások a kapcsolat nélküli munkaállomáson. Ez a lépés befejezéséhez a hardveres biztonsági modul inicializálási módban kell lennie. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>3.1. lépés: HSM módjának megváltoztatása "I"
Ha használ a Thales nShield él, állítsa át a módot: 1. A mód gomb segítségével jelölje ki a szükséges mód. 2. Néhány másodpercen belül tartsa lenyomva a világos gomb néhány másodpercig. A mód az új mód LED villogó leállítja, majd megvilágítottnak marad. Az állapot LED szabálytalan flash előfordulhat, hogy néhány másodpercig, és rendszeresen, amikor az eszköz készen áll majd tokenkódot. Ellenkező esetben a eszköz marad, a jelenlegi üzemmód, a megfelelő módba LED bekapcsolásával.

### <a name="step-32-create-a-security-world"></a>3.2. lépés: Biztonsági világ létrehozása
Nyisson meg egy parancsablakot, és futtassa a Thales új világot létrehozó programját.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Ez a program létrehoz egy **Biztonságivilág** fájl: % NFAST_KMDATA%\local\world, amely a C:\ProgramData\nCipher\Key Management Data\local mappának felel meg. A kvórumhoz különböző értékeket is használhat, de a fenti példában mindegyik három üres kártyát és PIN-kód megadását kéri. Ezután bármelyik két kártya teljes hozzáférést biztosít a biztonsági világhoz. Ezek a kártyák lesznek a **rendszergazdai Kártyakészlete** az új biztonsági világ számára.

Ezután tegye a következőket:

* Készítsen biztonsági másolatot a világfájlról. Biztonságos és a világfájlról, a rendszergazdai kártyák és a PIN-kódok védelmében, és győződjön meg arról, hogy egy személy önmagában nem fér hozzá egynél több kártyához.

### <a name="step-33-change-the-hsm-mode-to-o"></a>3.3. lépés: Módosítsa a HSM módba "O'
Ha használ a Thales nShield él, állítsa át a módot: 1. A mód gomb segítségével jelölje ki a szükséges mód. 2. Néhány másodpercen belül tartsa lenyomva a világos gomb néhány másodpercig. A mód az új mód LED villogó leállítja, majd megvilágítottnak marad. Az állapot LED szabálytalan flash előfordulhat, hogy néhány másodpercig, és rendszeresen, amikor az eszköz készen áll majd tokenkódot. Ellenkező esetben a eszköz marad, a jelenlegi üzemmód, a megfelelő módba LED bekapcsolásával.


### <a name="step-34-validate-the-downloaded-package"></a>3.4. lépés: A letöltött csomag ellenőrzése
Ez a lépés nem kötelező, de ajánlott a következők ellenőrzéséhez:

* Az eszközkészletben található kulcscserekulcs egy eredeti Thales HSM-en lett létrehozva.
* A biztonsági világának az eszközkészletben található kivonata egy eredeti Thales HSM-en lett létrehozva.
* A kulcscserekulcs nem exportálható.

> [!NOTE]
> A letöltött csomag ellenőrzéséhez a HSM csatlakoztatva kell lennie, kapcsolva, és rendelkeznie kell egy biztonsági világgal (például a most létrehozott).
>
>

A letöltött csomag ellenőrzése:

1. Futtassa a verifykeypackage.py parancsfájlt a következők egyike, attól függően, hogy a földrajzi régióban vagy Azure példánya beírásával:

   * Észak-amerikai:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Európa esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Ázsia esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Latin-Amerika: a

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * A japán:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Koreai:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Ausztráliában:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * A [Azure Government](https://azure.microsoft.com/features/gov/), az USA szövetségi példányát Azure használó:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Az Amerikai Egyesült Államok kormánya DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Kanada esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Németország:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * A India:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
     > [!TIP]
     > A Thales szoftver tartalmaz python %NFAST_HOME%\python\bin címen
     >
     >
2. Ellenőrizze, hogy látható-e a következő, az ellenőrzés sikerességét jelző: **Result: SUCCESS**

A parancsfájl ellenőrzi az aláírói láncot egészen a Thales-gyökérkulcsig. Ennek a gyökérkulcsnak a kivonata be van ágyazva a parancsfájlt, és az érték legyen **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Azt is ellenőrizheti ezt az értéket külön-külön látogasson el a [Thales webhelyén](http://www.thalesesec.com/).

Most már készen áll egy új kulcsot létrehozni.

### <a name="step-35-create-a-new-key"></a>3.5. lépés: Új kulcs létrehozása
Hozzon létre egy kulcsot a Thales használatával **generatekey** program.

A következő parancsot a kulcs létrehozásához:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Ez a parancs futtatásakor használja ezeket az utasításokat:

* A paraméter *védelme* értékre kell állítani **modul**látható módon. Ez a modul által védett kulcsot hoz létre. A BYOK eszközkészlet nem támogatja az OCS által védett kulcsokat.
* Cserélje le a *contosokey* a a **ident** és **plainname** bármilyen karakterlánc-értékkel. Adminisztratív terhek minimalizálása érdekében, és csökkenthető a kockázata, hibák, azt javasoljuk, hogy mindkét használja ugyanazt az értéket. A **ident** érték csak számokat, kötőjeleket és kisbetűket tartalmazhat.
* A pubexp üresen maradt (alapértelmezett) ebben a példában, de az adott értékeket adhat meg. További információ a Thales dokumentációjában talál.

Ez a parancs egy tokenekre bontott kulcsfájlt hoz létre az %NFAST_KMDATA%\local mappában, amelynek a neve a **key_simple_**, utána pedig a **ident** a parancsban megadott. Például: **key_simple_contosokey**. Ez a fájl egy titkosított kulcsot tartalmaz.

Készítsen biztonsági másolatot a tokenekre bontott Kulcsfájlról egy biztonságos helyre.

> [!IMPORTANT]
> Amikor később átviszi a kulcsot az Azure Key Vaultba, Microsoft kulcs nem exportálható a azt, rendkívül fontos, hogy biztonsági másolatot készíteni a kulcs és a biztonsági világáról biztonságosan. Lépjen kapcsolatba a Thales útmutatás és ajánlott eljárások a kulcs biztonsági mentéséről.
>
>

Most már készen áll a kulcs átvitele az Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>4. lépés: A kulcs Áthelyezés előkészítése
A negyedik lépése hajtsa végre az alábbi eljárások a kapcsolat nélküli munkaállomáson.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>4.1. lépés: A korlátozott engedélyekkel rendelkező másolata a létrehozása

Nyisson meg egy új parancssort, és módosítsa az aktuális könyvtárhoz a helyet, ahol unzipped a BYOK zip-fájlban. A kulcs engedélyek korlátozásához a parancssorból, futtassa a következők egyike, attól függően, hogy a földrajzi régióban vagy Azure példánya:

* Észak-amerikai:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Európa esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Ázsia esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Latin-Amerika: a

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* A japán:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Koreai:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Ausztráliában:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* A [Azure Government](https://azure.microsoft.com/features/gov/), az USA szövetségi példányát Azure használó:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Az Amerikai Egyesült Államok kormánya DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Kanada esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Németország:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* A India:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1

Ez a parancs futtatásakor cserélje le *contosokey* ugyanarra az értékre az **lépés 3.5: hozzon létre egy új kulcsot** a a [a kulcs létrehozása](#step-3-generate-your-key) lépés.

A rendszer felkéri a biztonsági globális rendszergazdai kártyák csatlakoztassa.

A parancs befejeződésekor megjelenik **eredmény: sikeres** és a kulcs korlátozott engedélyekkel rendelkező másolata a key_xferacId_ nevű fájlban található<contosokey>.

Előfordulhat, hogy megvizsgálja, hozzáférés-vezérlési LISTÁK használata a következő parancsokat a Thales segédprogramjai használatával:

* aclprint.PY:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Ezek a parancsok futtatásakor cserélje le contosokey megadott azonos értékű **lépés 3.5: hozzon létre egy új kulcsot** a a [a kulcs létrehozása](#step-3-generate-your-key) lépés.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>4.2. lépés: A kulcs titkosítása a Microsoft kulcscserekulcs használatával
Attól függően, hogy a földrajzi régióban vagy Azure példánya a következő parancsok egyikét futtatja:

* Észak-amerikai:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Európa esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Ázsia esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Latin-Amerika: a

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* A japán:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Koreai:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Ausztráliában:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* A [Azure Government](https://azure.microsoft.com/features/gov/), az USA szövetségi példányát Azure használó:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Az Amerikai Egyesült Államok kormánya DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Kanada esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Németország:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* A India:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Ez a parancs futtatásakor használja ezeket az utasításokat:

* Cserélje le *contosokey* az azonosítóra, amelyet a kulcs létrehozásához használt **lépés 3.5: hozzon létre egy új kulcsot** a a [a kulcs létrehozása](#step-3-generate-your-key) lépés.
* Cserélje le *SubscriptionID* , azonosító: az Azure-előfizetés, amely a kulcstartót tartalmazza. Ezt az értéket lekért korábban a **1.2-es lépés: az Azure-előfizetés beszerzése** a a [az internetre kapcsolódó munkaállomás előkészítése](#step-1-prepare-your-internet-connected-workstation) lépés.
* Cserélje le *ContosoFirstHSMKey* a címkére, amelyet a kimeneti fájl neveként használatos.

Amikor ez befejeződik, megjeleníti **eredmény: sikeres** és egy új fájlt az aktuális mappában, amely a következő névvel: KeyTransferPackage -*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>4.3. lépés: A kulcsátviteli csomag másolása az internetre kapcsolódó munkaállomásra
Egy USB-meghajtó vagy más hordozható tárolóeszköz használatával másolja a kimeneti fájl az előző lépésben (KeyTransferPackage-ContosoFirstHSMkey.byok) az internetre kapcsolódó munkaállomás.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>5. lépés: A kulcs átvitele az Azure Key Vault
Az utolsó lépést, az internetre kapcsolódó munkaállomáson, használja a [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) parancsmag fájlból másolt a kapcsolat nélküli munkaállomáson az Azure Key Vault HSM kulcsátviteli csomag feltöltéséhez:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Sikeres feltöltés esetén megjelenik a most felvett kulcs tulajdonságainak jelenik meg.

## <a name="next-steps"></a>Következő lépések
A HSM által védett kulcs key vaultban lévő most már használhatja. További információkért lásd: a **Ha hardveres biztonsági modul (HSM) használni kívánt** szakasz a [Ismerkedés az Azure Key Vault](key-vault-get-started.md) oktatóanyag.
