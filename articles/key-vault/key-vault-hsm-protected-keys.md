---
title: Generate and transfer HSM által védett kulcsok az Azure Key Vault – Azure Key Vault és hogyan |} A Microsoft Docs
description: Ez a cikk segítségével tervezése, létrehozása, és utána a saját HSM által védett kulcsok az Azure Key Vault használata. Más néven BYOK vagy a saját kulcs használata.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 76943c89cd4c0a283dc36a2a0d28c907cef0ad28
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114688"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Generate and transfer HSM-védelemmel ellátott és hogyan lehet az Azure Key Vault-kulcsok

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A hozzáadott frissítési garanciát biztosító Azure Key Vault használata esetén meg is kulcsok importálását vagy létrehozását hardveres biztonsági modulokban (HSM) a HSM határait betartó. Ebben a forgatókönyvben gyakran nevezik *saját kulcs használata*, azaz BYOK. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Az Azure Key Vault Ön kulcsainak védelmét Thales nShield hardveres biztonsági modulok-család használ.

Ebben a témakörben található információk használatával tervezése, létrehozása, és utána a saját HSM által védett kulcsok az Azure Key Vault használata.

Ez a funkció nem érhető el az Azure China.

> [!NOTE]
> Azure Key Vaulttal kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](key-vault-whatis.md)  
> Egy első lépéseket megkönnyítő oktatóanyagunkat, amely tartalmazza a key vault HSM által védett kulcsok létrehozása, lásd: [Mi az Azure Key Vault?](key-vault-overview.md).

További információ és a egy HSM által védett kulcs átvitele az interneten keresztül:

* Egy kapcsolat nélküli munkaállomáson, ami csökkenti a támadási felület hozza létre a kulcsot.
* A kulcs titkosítva van az egy kulcs kulcscserekulcs-(KEK), amely mindaddig, amíg az Azure Key Vault HSM való átvitelig titkosítva marad. Csak a kulcs titkosított verziója hagyja el az eredeti munkaállomást.
* Az eszközkészlet, amely lekötné a kulcsot az Azure Key Vault biztonsági világába a bérlőkulcs tulajdonságainak beállítása. Így miután az Azure Key Vault HSM kap, és a kulcs visszafejtéséhez, csak a HSM-EK is használható. A kulcs nem exportálható. Ezt a kötést a Thales HSM-EK kikényszeríti a.
* A kulcscserekulcs (KEK) a kulcs titkosításához használt jön létre az Azure Key Vault HSM belül, és nem exportálható. A HSM-EK kényszerítéséhez, hogy az a HSM-EK kívül a KEK nem létezhet titkosítatlan verziója lehet. Emellett az eszközkészlet tartalmaz igazolási Thales, hogy a KEK nem exportálható, és egy eredeti HSM, amely a Thales által gyártott lett létrehozva.
* Az eszközkészlet, hogy az Azure Key Vault biztonsági világ szintén létrejött egy eredeti Thales által gyártott HSM a Thales igazolást tartalmaz. Ez a tanúsítvány igazolja, Önnek, hogy a Microsoft eredeti hardvereket használ.
* A Microsoft külön kek használja, és biztonsági világot minden egyes földrajzi régióban. Ez a fajta elkülönítés biztosítja, hogy használható-e a kulcsot csak az adatközpontokban a régióban, ahol titkosítva. Például egy Európai ügyfél kulcs nem használható Észak-amerikai vagy ázsiai adatközpontokban.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>További információt a Thales HSM-ekről és a Microsoft-szolgáltatások

A Thales e-Security vezető globális szolgáltató, amely adattitkosítási és internetes biztonsági megoldásokat a pénzügyi szolgáltatások, a csúcstechnológiai, a gyártási, a kormányzati és a technológiai szektorok részére. Egy 40 éves követése rekord, védi a vállalati és kormányzati információk a Thales megoldásait négy az öt legnagyobb energetikai és a légi közlekedésben cég által használt. Megoldásaikat is használja a 22 NATO országok, és biztonságos globális fizetési tranzakciók több mint 80 %-át.

A Microsoft rendelkezik Thales vállalattal együttműködve folyamatosan javíthatja a legkorszerűbb HSM-eket. Ezeknek a fejlesztéseknek köszönhetően, hogy feláldoznia a kulcsai nélkül üzemeltetett szolgáltatások szokásos előnyeit. Pontosabban a fejlesztéseknek köszönhetően a Microsoft felügyelje a HSM-eket, hogy az nem rendelkezik. Felhőalapú szolgáltatásként az Azure Key Vault felskálázással, a szervezet használati csúcsok növekedésekhez. Egy időben a kulcs HSM által védett: Meg kell őriznie a kulcs életciklusa fölötti ellenőrzés, mert a kulcs létrehozásához, és vigye át a Microsoft HSM-EK.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>A saját kulcs (használatának BYOK) megvalósítása használata az Azure Key Vault

A következő információkat és eljárásokat használhatja a saját HSM-védelemmel ellátott kulcs generálása és átvitele az Azure Key Vault – a saját eszközök használata a saját kulcs (használatának BYOK) forgatókönyv.

## <a name="prerequisites-for-byok"></a>A BYOK előfeltételei

Tekintse meg az alábbi táblázat a saját kulcs használata (BYOK) for Azure Key Vault szükséges előfeltételek listáját.

| Követelmény | További információ |
| --- | --- |
| Azure-előfizetéssel |Hozzon létre egy Azure Key Vaultban, Azure-előfizetés szükséges: [Regisztráljon az ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/) |
| Az Azure Key Vault Premium szolgáltatási szinten a HSM-védelemmel ellátott kulcsok támogatására |Az Azure Key Vault a szolgáltatási szintek és a képességekkel kapcsolatos további információkért lásd: a [Azure Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) webhelyén. |
| A Thales HSM, intelligens kártyák és támogatószoftver |A Thales hardveres biztonsági modul és a Thales HSM-EK alapvető ismeretekre hozzáféréssel kell rendelkeznie. Lásd: [Thales hardveres biztonsági modul](https://www.thales-esecurity.com/msrms/buy) kompatibilis modellek vagy HSM vásárlásához, ha nem rendelkezik egy listája. |
| A következő hardverre és szoftverekre:<ol><li>Egy kapcsolat nélküli x64 munkaállomáson, egy minimális Windows operációs rendszer, legalább Windows 7 és a Thales nShield szoftver verziója 11.50.<br/><br/>Ha ez a munkaállomás Windows 7 fut, meg kell [Microsoft .NET-keretrendszer 4.5-ös verziójának telepítése](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Egy munkaállomás, amely csatlakozik az internethez, és legalább Windows operációs rendszert Windows 7 és [Azure PowerShell-lel](/powershell/azure/overview?view=azps-1.2.0) **minimális 1.1.0-s** telepítve.</li><li>USB-meghajtóra vagy más hordozható tárolóeszköz, amelyen legalább 16 MB szabad tárhely.</li></ol> |Biztonsági okokból javasoljuk, hogy az első munkaállomás nincs csatlakoztatva a hálózathoz. Azonban ez a javaslat nem szoftveres követelmény.<br/><br/>Az alábbi utasításokat, az ezen a munkaállomáson nevezzük a kapcsolat nélküli munkaállomáson.</p></blockquote><br/>Ezenkívül ha a bérlői kulcsot egy éles hálózati környezetben, azt javasoljuk, hogy egy második, különálló munkaállomást használjon az eszközkészlet letöltése, és a bérlőkulcs feltöltésére. De tesztelési célokra használhatja munkaállomást az elsőt.<br/><br/>Az alábbi utasításokat, az erre a második munkaállomásra nevezzük az internetre kapcsolódó munkaállomásra.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Hozzon létre, és a kulcs átvitele az Azure Key Vault HSM-be

Az alábbi öt lépést használatával fog létrehozni, és a kulcs átvitele az Azure Key Vault HSM:

* [1. lépés: Az internetre kapcsolódó munkaállomás előkészítése](#step-1-prepare-your-internet-connected-workstation)
* [2. lépés: A kapcsolat nélküli munkaállomás előkészítése](#step-2-prepare-your-disconnected-workstation)
* [3. lépés: A kulcs létrehozásához](#step-3-generate-your-key)
* [4. lépés: A kulcs előkészítése az átvitelre](#step-4-prepare-your-key-for-transfer)
* [5. lépés: A kulcs átvitele az Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>1. lépés: Az internetre kapcsolódó munkaállomás előkészítése

Első lépésként hajtsa végre az alábbi eljárásokat az internethez csatlakoztatott munkaállomáson.

### <a name="step-11-install-azure-powershell"></a>1.1. lépés: Az Azure PowerShell telepítése

Az internetkapcsolattal rendelkező munkaállomáson, a töltse le és telepítse az Azure PowerShell-modult, amely tartalmazza a parancsmagok az Azure Key Vault kezeléséhez. A telepítési utasításokért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>1.2. lépés: Az Azure előfizetés-azonosító beszerzése

Indítsa el az Azure PowerShell-munkamenetet, és jelentkezzen be Azure-fiókjába a következő parancs használatával:

```Powershell
   Connect-AzAccount
```
Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Ezután használja a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsot:

```powershell
   Get-AzSubscription
```
A kimenetben keresse meg azt az előfizetést, használhatja az Azure Key Vault azonosítója. Később szüksége lesz az előfizetés-azonosító.

Ne zárja be az Azure PowerShell-ablakot.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>1.3. lépés: Az Azure Key Vault a BYOK eszközkészlet letöltése

Nyissa meg a Microsoft Download Center és [az Azure Key Vault BYOK eszközkészlet letöltésére](https://www.microsoft.com/download/details.aspx?id=45345) a földrajzi régió vagy Azure példányát. Az alábbi információk segítségével azonosíthatja a csomag nevét, letöltése és a megfelelő SHA-256-csomag kivonata:

- - -
**Egyesült Államok:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**Európa:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**Ázsia:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

- - -
**Latin-Amerika:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

- - -
**Japán:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

- - -
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

- - -
**Ausztrália:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Az Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**Egyesült Államok kormányának DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

- - -
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

- - -
**Németország:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

- - -
**India:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

- - -
**Franciaország:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

- - -
**Egyesült Királyság:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

A letöltött BYOK eszközkészlet, az Azure PowerShell-munkamenetben sértetlenségének ellenőrzéséhez használja a [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) parancsmagot.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Az eszközkészlet a következőket tartalmazza:

* Egy kulcscserekulcs (KEK) csomag, amelynek a neve **BYOK-KEK - pkg-.**
* Egy Biztonságivilág-csomag, amelynek a neve **BYOK-SecurityWorld - pkg-.**
* Egy python-szkriptet nevű **verifykeypackage.py.**
* Egy parancssori végrehajtható fájl **KeyTransferRemote.exe** és kapcsolódó dll-fájlok.
* Egy Visual C++ terjeszthető csomag, nevű **vcredist_x64.exe.**

Másolja a csomagot egy USB-meghajtó vagy más hordozható tárolóeszközre.

## <a name="step-2-prepare-your-disconnected-workstation"></a>2. lépés: A kapcsolat nélküli munkaállomás előkészítése

A második lépés hajtsa végre a következő eljárások a munkaállomáson, amely nem csatlakozik a hálózathoz (az interneten vagy belső hálózaton).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>2.1. lépés: A Thales HSM-mel a kapcsolat nélküli munkaállomás előkészítése

Telepítse az nCipher (Thales) egy Windows-számítógépen, majd csatolja a számítógéphez egy Thales HSM.

Győződjön meg arról, hogy a Thales-eszközök az elérési úthoz (**%nfast_home%\bin**). Írja be például a következőket:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

További információkért tekintse meg a Thales HSM-mel mellékelt felhasználói útmutatót.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>2.2. lépés: A kapcsolat nélküli munkaállomáson a BYOK eszközkészlet telepítése

Másolja a BYOK eszközkészletcsomagot az USB-meghajtóra vagy egyéb hordozható tárolóeszköz, és tegye a következőket:

1. Csomagolja ki a fájlokat a letöltött csomag egy tetszőleges mappába.
2. A mappából futtassa a vcredist_x64.exe.
3. Kövesse az utasításokat a telepítés a Visual C++ futtatókörnyezeti összetevők Visual Studio 2013-hoz készült.

## <a name="step-3-generate-your-key"></a>3. lépés: A kulcs létrehozásához

A harmadik lépést hajtsa végre a következő eljárások a kapcsolat nélküli munkaállomáson. E lépés elvégzése után a HSM inicializálási módban kell lennie. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>3.1. lépés: A HSM mód megváltoztatása "I"

Ha Edge, a Thales nShield módjának módosításához használja: 1. Jelölje ki a szükséges módot az üzemmód gomb használatával. 2. Néhány másodpercen belül tartsa lenyomva a Törlés gomb egy pár másodpercet. Ha módosítja a módot, az új mód LED villogó leállítja, és megvilágítottnak marad. Az állapot LED szabálytalan flash előfordulhat, hogy néhány másodpercet, és rendszeresen, amikor az eszköz készen áll majd tokenkódot. Ellenkező esetben az eszköz továbbra is az aktuális módban, a megfelelő módot LED bekapcsolásával.

### <a name="step-32-create-a-security-world"></a>3.2. lépés: Biztonsági világ létrehozása

Indítson el egy parancssort, és futtassa a Thales új világot létrehozó programját.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3
   ```

Ez a program létrehoz egy **Biztonságivilág** fájlban a következő % NFAST_KMDATA%\local\world, amely a C:\ProgramData\nCipher\Key Management Data\local mappának felel meg. A kvórum különböző értékeket is használhat, de ebben a példában, meg kell adnia három üres kártyát és PIN-kód egyes. Ezután bármelyik két kártya teljes hozzáférést biztosít a biztonsági világhoz. Ezek a kártyák lesznek az **rendszergazdai Kártyakészlete** az új biztonsági világ számára.

> [!NOTE]
> Ha a HSM támogatja az újabb rejtjelzésben suite DLf3072s256mRijndael, lecserélheti ezen--titkosítócsomag DLf1024s160mRijndael = az--titkosítócsomag = DLf3072s256mRijndael

Ezután tegye a következőket:

* Készítsen biztonsági másolatot a világfájlról. Biztonságos és védi a világfájlról, a rendszergazdai kártyák és a PIN-kódok, és győződjön meg arról, hogy egyetlen személy nem rendelkezik-e egynél több kártyához való hozzáférést.

### <a name="step-33-change-the-hsm-mode-to-o"></a>3.3. lépés: A HSM mód megváltoztatása "O'

Ha Edge, a Thales nShield módjának módosításához használja: 1. Jelölje ki a szükséges módot az üzemmód gomb használatával. 2. Néhány másodpercen belül tartsa lenyomva a Törlés gomb egy pár másodpercet. Ha módosítja a módot, az új mód LED villogó leállítja, és megvilágítottnak marad. Az állapot LED szabálytalan flash előfordulhat, hogy néhány másodpercet, és rendszeresen, amikor az eszköz készen áll majd tokenkódot. Ellenkező esetben az eszköz továbbra is az aktuális módban, a megfelelő módot LED bekapcsolásával.

### <a name="step-34-validate-the-downloaded-package"></a>3.4. lépés: A letöltött csomag ellenőrzése

Ez a lépés nem kötelező, azonban ajánlott, így ellenőrizheti, hogy a következő:

* Az eszközkészletben található kulcscserekulcs egy eredeti Thales HSM-en lett létrehozva.
* A biztonsági világának az eszközkészletben található kivonata egy eredeti Thales HSM lett létrehozva.
* A kulcscserekulcs nem exportálható.

> [!NOTE]
> A letöltött csomag ellenőrzéséhez a HSM csatlakoztatva kell lennie, bekapcsolt, és rendelkeznie kell egy biztonsági világgal (például az imént létrehozott,).

A letöltött csomag ellenőrzése:

1. Futtassa a verifykeypackage.py parancsfájlt írja be a következők egyikét, attól függően, a földrajzi régió vagy Azure példányát:

   * Észak-Amerikában:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Európa esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Ázsia esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Latin-Amerikában:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * A japán:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Koreai:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Ausztrália:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * A [Azure Government](https://azure.microsoft.com/features/gov/), amely az Azure US government példányát használja:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Az USA kormányzatának DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Kanada:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Németország:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Az indiai:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Franciaország:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Egyesült Királyság:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1

     > [!TIP]
     > A Thales szoftver tartalmaz python %NFAST_HOME%\python\bin címen
     >
     >
2. Győződjön meg arról, hogy megjelenik-e a következő, az ellenőrzés sikerességét jelző: **Eredmény: SUCCESS**

A parancsfájl ellenőrzi az aláírói láncot egészen a Thales-gyökérkulcsig. Ennek a gyökérkulcsnak a kivonata be van ágyazva a szkriptbe és a hozzá tartozó értéket kell **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Azt is ellenőrizheti ezt az értéket külön-külön meglátogatják a [Thales webhelyén](http://www.thalesesec.com/).

Most már készen áll egy új kulcsot létrehozni.

### <a name="step-35-create-a-new-key"></a>3.5. lépés: Új kulcs létrehozása

Hozzon létre egy kulcsot a Thales használatával **Kulcslétrehozási** program.

Futtassa a következő parancsot a kulcs létrehozásához:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Ez a parancs futtatásakor használja a következő utasításokat:

* A paraméter *védelme* értékre kell állítani **modul**látható módon. Ez a modul által védett kulcsot hoz létre. A BYOK eszközkészlet nem támogatja az OCS által védett kulcsokat.
* Értékét cserélje *contosokey* számára a **ident** és **plainname** rendelkező bármilyen karakterlánc típusú értéket. Az adminisztratív terhek minimalizálása és a hibák kockázatának csökkentése érdekében azt javasoljuk, hogy mindkettőhöz azonos értéket használjon. A **ident** értéknek kell szerepelnie, csak számokat, kötőjeleket és kisbetűket tartalmazhat.
* A pubexp üresen (alapértelmezett) ebben a példában, de megadhat specifikus értékeket. További információk a Thales dokumentációjában talál.

Ez a parancs egy tokenekre bontott kulcsfájlt hoz létre az %NFAST_KMDATA%\local mappában, neve **key_simple_**, majd a **ident** , amely a parancsban megadott. Például: **key_simple_contosokey**. Ez a fájl egy titkosított kulcsot tartalmaz.

Készítsen biztonsági másolatot a tokenekre bontott Kulcsfájlról egy biztonságos helyre.

> [!IMPORTANT]
> Amikor később átviszi a kulcsot az Azure Key Vaultba, a Microsoft nem tudja exportálni ezt a kulcsot vissza, ezért rendkívül fontos, hogy, biztonsági másolatot készítsen a kulcsáról és a biztonsági világáról. Lépjen kapcsolatba a Thales útmutatás és ajánlott eljárások a kulcs biztonsági mentéséről.
>


Most már készen áll a kulcs átvitele az Azure Key Vaultban.

## <a name="step-4-prepare-your-key-for-transfer"></a>4. lépés: A kulcs előkészítése az átvitelre

A negyedik lépést hajtsa végre a következő eljárások a kapcsolat nélküli munkaállomáson.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>4.1. lépés: Hozzon létre egy másolatot a kulcsról korlátozott engedélyekkel

Nyisson meg egy új parancssort, és módosítsa az aktuális könyvtárban, a kicsomagolt, ahol a BYOK zip-fájl helyét. A kulcs kapcsolatos engedélyek korlátozásához a parancsot a parancssorba, futtassa a következők egyikét, attól függően, a földrajzi régió vagy Azure példányát:

* Észak-Amerikában:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Európa esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Ázsia esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Latin-Amerikában:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* A japán:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Koreai:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Ausztrália:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* A [Azure Government](https://azure.microsoft.com/features/gov/), amely az Azure US government példányát használja:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Az USA kormányzatának DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Kanada:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Németország:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Az indiai:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Franciaország:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Egyesült Királyság:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1

Ez a parancs futtatásakor cserélje le *contosokey* ugyanarra az értékre az **3.5. lépés: Hozzon létre egy új kulcsot** származó a [hozhatja létre a kulcsot](#step-3-generate-your-key) . lépés.

A rendszer felkéri a security world rendszergazdai kártyák beépülő modul.

A parancs befejeződésekor megjelenik **eredménye: SIKERES** és a kulcs korlátozott engedélyekkel rendelkező másolata a key_xferacId_ nevű fájlban található<contosokey>.

Előfordulhat, hogy megvizsgálja a következő parancsokat a Thales segédprogramjai használatával hozzáférés-vezérlési LISTÁK használatával:

* aclprint.PY:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Ezek a parancsok futtatásakor cserélje le contosokey ugyanazt az értéket a megadott **3.5. lépés: Hozzon létre egy új kulcsot** származó a [hozhatja létre a kulcsot](#step-3-generate-your-key) . lépés.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>4.2. lépés: A kulcs titkosítása a Microsoft kulcscserekulcs használatával

Futtassa a következő parancsokat, földrajzi régió vagy az Azure példány egyikét:

* Észak-Amerikában:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Európa esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Ázsia esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Latin-Amerikában:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* A japán:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Koreai:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Ausztrália:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* A [Azure Government](https://azure.microsoft.com/features/gov/), amely az Azure US government példányát használja:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Az USA kormányzatának DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Kanada:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Németország:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Az indiai:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Franciaország:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Egyesült Királyság:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Ez a parancs futtatásakor használja a következő utasításokat:

* Cserélje le *contosokey* az azonosítóra, amelyet a kulcs létrehozásához használt **3.5. lépés: Hozzon létre egy új kulcsot** származó a [hozhatja létre a kulcsot](#step-3-generate-your-key) . lépés.
* Cserélje le *SubscriptionID* azonosítójú, az Azure-előfizetést, amely tartalmazza a key vaultban. Ez az érték lekért korábban a **1.2. lépés: Az Azure-előfizetés Azonosítójának lekéréséhez** származó a [az internetre kapcsolódó munkaállomás előkészítése](#step-1-prepare-your-internet-connected-workstation) . lépés.
* Cserélje le *ContosoFirstHSMKey* a kimeneti fájl neveként használt címkével.

Amikor ez befejeződik, megjelenik **eredménye: SIKERES** már van egy új fájlt a jelenlegi mappában a következő névvel: KeyTransferPackage -*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>4.3. lépés: A kulcsátviteli csomag másolása az internetre kapcsolódó munkaállomásra

USB-meghajtóra vagy egyéb hordozható tárolóeszköz használatával másolja a kimeneti fájlt az előző lépésben (KeyTransferPackage-ContosoFirstHSMkey.byok) az internetre kapcsolódó munkaállomáson.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>5. lépés: A kulcs átvitele az Azure Key Vault

Ez az utolsó lépés az internethez csatlakozó munkaállomáson használja a [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag, amely az Azure Key Vault HSM-be a kapcsolat nélküli munkaállomásról másolt kulcsátviteli csomag feltöltéséhez:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Sikeres feltöltés esetén, tekintse meg a tulajdonságait, a kulcs, amelyet az imént hozzáadott jelenik meg.

## <a name="next-steps"></a>További lépések

Mostantól használhatja ezt a HSM által védett kulcsot tárol a kulcstárolóban. További információkért lásd: a **Ha hardveres biztonsági modul (HSM) használni kívánt** című rész a [Ismerkedés az Azure Key Vault](key-vault-overview.md) oktatóanyag.
