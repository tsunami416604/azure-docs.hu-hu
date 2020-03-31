---
title: HSM-védelemmel ellátott kulcsok létrehozása és átvitele az Azure Key Vaulthoz – Azure Key Vault | Microsoft dokumentumok
description: Ebből a cikkből segítséget tervezhet, hozhat létre, majd vihetát át saját HSM-védelemmel ellátott kulcsait az Azure Key Vault használatával. Más néven BYOK, vagy hogy a saját kulcsát.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 0d51e1aaf1d9b0472245b9a7f29148517a0a7b93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77429305"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>HSM-védelemmel ellátott kulcsok importálása a Key Vaulthoz (örökölt)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A további biztonság érdekében az Azure Key Vault használatakor importálhat vagy létrehozhat kulcsokat a hardveres biztonsági modulokban (HSM-ekben), amelyek soha nem hagyják el a HSM-határt. Ennek a megoldásnak a neve *saját kulcs használata*, angol betűszóval BYOK. Az Azure Key Vault nCipher nShield HSM-család (FIPS 140-2 Level 2 érvényesített) a kulcsok védelme érdekében.

Az ebben a témakörben található információk segítségével megtervezheti, létrehozhatja, majd átviheti saját HSM-védelemmel ellátott kulcsait az Azure Key Vault használatával.

Ez a funkció nem érhető el az Azure China.

> [!NOTE]
> Az Azure Key Vaultról a [Mi az Azure Key Vault?](key-vault-overview.md)  
> Az első lépések oktatóanyag, amely magában foglalja a HSM által védett kulcsok key vault létrehozása, [lásd: Mi az Azure Key Vault?](key-vault-overview.md)

További információ a HSM-védelemmel ellátott kulcs interneten keresztültörténő létrehozásáról és átviteléről:

* A kulcsot egy offline munkaállomásról hozza létre, ami csökkenti a támadási felületet.
* A kulcs titkosítva van egy kulcscsere-kulcs (KEK), amely titkosítva marad, amíg át nem kerül az Azure Key Vault HSMs. Csak a kulcs titkosított verziója hagyja el az eredeti munkaállomást.
* Az eszközkészlet beállítja a bérlői kulcs tulajdonságait, amelyek a kulcsot az Azure Key Vault biztonsági világához kötik. Így miután az Azure Key Vault HSM-ek fogadása és visszafejtése a kulcs, csak ezek a HSM-ek használhatják. A kulcs nem exportálható. Ezt a kötést az nCipher HSM-ek kényszerítik.
* A kulcs titkosítására használt kulcscserekulcs (KEK) az Azure Key Vault HSMs-en belül jön létre, és nem exportálható. A HSM-ek biztosítják, hogy a KEK-nek nem létezhet titkosítatlan verziója a HSM-eken kívül. Ezenkívül az eszközkészlet tartalmazza az nCipher tanúsítványát, hogy a KEK nem exportálható, és egy eredeti HSM-ben keletkezett, amelyet az nCipher gyártott.
* Az eszközkészlet tartalmazza az nCipher tanúsítványát, amely szerint az Azure Key Vault biztonsági világa is létrejött egy eredeti HSM-en, amelyet az nCipher gyártott. Ez az igazolás bizonyítja, hogy a Microsoft eredeti hardvert használ.
* A Microsoft minden földrajzi régióban külön KEK-ket és különálló biztonsági világokat használ. Ez a szétválasztás biztosítja, hogy a kulcs csak abban a régióban lévő adatközpontokban használható, ahol titkosította. Egy európai ügyfél kulcsa például nem használható észak-amerikai vagy ázsiai adatközpontokban.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>További információ az nCipher HSM-ekről és a Microsoft-szolgáltatásokról

NCipher Security, az Entrust Datacard vállalat, vezető szerepet tölt be az általános célú HSM piacon, amely a világ vezető szervezeteit képessé teszi azáltal, hogy bizalmat, integritást és ellenőrzést biztosít üzleti kritikus információik és alkalmazásaik számára. Az nCipher kriptográfiai megoldásai biztosítják a kialakulóban lévő technológiákat – felhő, IoT, blockchain, digitális fizetések –, és segítenek az új megfelelőségi megbízások teljesítésében, ugyanazt a bevált technológiát használva, amelytől a globális szervezetek ma támaszkodnak, hogy megvédjék őket a érzékeny adatok, a hálózati kommunikáció és a vállalati infrastruktúra. Az nCipher bizalmat biztosít az üzleti legkritikusabb alkalmazások ban, biztosítva az adatok integritását, és teljes ellenőrzést biztosítva az ügyfelek számára – ma, holnap, mindig.

A Microsoft együttműködött az nCipher Security-szel a HSM-ek korszerű állapotának javítása érdekében. Ezeknek a fejlesztéseknek köszönhetően Ön úgy élvezheti az üzemeltetett szolgáltatások szokásos előnyeit, hogy közben nem kell feláldoznia a kulcsai felügyeletét. Egészen pontosan a fejlesztéseknek köszönhetően a Microsoft képes felügyelni a HSM-eket, hogy Önnek ne kelljen. Felhőszolgáltatásként az Azure Key Vault rövid időn belül felskálázódik, hogy megfeleljen a szervezet használati csúcsainak. Mindeközben a Microsoft HSM-jei biztosítják az Ön kulcsának védelmét: a kulcs életciklusa fölötti felügyelet az Ön kezében marad, mivel a kulcsot Ön hozza létre és továbbítja a Microsoft HSM-jeire.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Saját kulcs használatának (BYOK) megvalósítása az Azure Key Vaulthoz

Használja az alábbi információkat és eljárásokat, ha saját HSM-védelemmel ellátott kulcsot hoz létre, majd átviszi az Azure Key Vaultba – a saját kulcs (BYOK) forgatókönyvet.

## <a name="prerequisites-for-byok"></a>A BYOK előfeltételei

Az alábbi táblázatban megtekintheti a saját kulcs (BYOK) azure Key Vaulthoz való hozásának előfeltételeit.

| Követelmény | További információ |
| --- | --- |
| Előfizetés az Azure-ra |Az Azure Key Vault létrehozásához Azure-előfizetésre van szüksége: [Regisztráljon az ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/) |
| Az Azure Key Vault premium szolgáltatási szint a HSM által védett kulcsok támogatásához |Az Azure Key Vault szolgáltatási rétegeiről és képességeiről az [Azure Key Vault díjszabási](https://azure.microsoft.com/pricing/details/key-vault/) webhelyén talál további információt. |
| nCipher nShield HSMs, intelligens kártyák és támogató szoftverek |Hozzáféréssel kell rendelkeznie egy nCipher hardverbiztonsági modulhoz és az nCipher nShield HSMs alapvető működési ismereteihez. Lásd [nCipher nShield Hardver biztonsági modul](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) a listát a kompatibilis modellek, vagy vásárolni egy HSM, ha nem rendelkezik ilyen. |
| A következő hardver ek és szoftverek:<ol><li>Offline x64 munkaállomás, amelynek windows 7 és nCipher nShield szoftvere legalább 11.50-es verziójú Windows operációs rendszerrel rendelkezik.<br/><br/>Ha ez a munkaállomás Windows 7 rendszert futtat, [telepítenie](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)kell a Microsoft .</li><li>Olyan munkaállomás, amely csatlakozik az internethez, és rendelkezik a Windows 7 minimális Windows operációs rendszerével és az [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **1.1.0-s verziójával.**</li><li>Legalább 16 MB szabad területtel rendelkező USB-meghajtó vagy más hordozható tárolóeszköz.</li></ol> |Biztonsági okokból javasoljuk, hogy az első munkaállomás ne csatlakozzon hálózathoz. Ez az ajánlás azonban nincs programozott módon érvényesítve.<br/><br/>Az alábbi utasításokban ezt a munkaállomást leválasztott munkaállomásnak nevezzük.</p></blockquote><br/>Emellett ha a bérlői kulcs egy éles hálózathoz, azt javasoljuk, hogy egy második, külön munkaállomás t használja az eszközkészlet letöltéséhez, és töltse fel a bérlői kulcsot. Tesztelési célokra azonban használhatja az első munkaállomást is.<br/><br/>Az alábbi utasításokban ezt a második munkaállomást internetkapcsolattal rendelkező munkaállomásnak nevezzük.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Kulcs létrehozása és átvitele az Azure Key Vault HSM-be

A következő öt lépéssel hozza létre és viheti át a kulcsot egy Azure Key Vault HSM-be:

* [1. lépés: Az internethez csatlakoztatott munkaállomás előkészítése](#step-1-prepare-your-internet-connected-workstation)
* [2. lépés: A leválasztott munkaállomás előkészítése](#step-2-prepare-your-disconnected-workstation)
* [3. lépés: A kulcs létrehozása](#step-3-generate-your-key)
* [4. lépés: A kulcs előkészítése az átvitelre](#step-4-prepare-your-key-for-transfer)
* [5. lépés: A kulcs átvitele az Azure Key Vaultba](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>1. lépés: Az internethez csatlakoztatott munkaállomás előkészítése

Ehhez az első lépéshez tegye a következő eljárásokat az internethez csatlakoztatott munkaállomáson.

### <a name="step-11-install-azure-powershell"></a>1.1. lépés: Az Azure PowerShell telepítése

Az internethez csatlakoztatott munkaállomásról töltse le és telepítse az Azure PowerShell-modult, amely tartalmazza az Azure Key Vault kezeléséhez szükséges parancsmagokat. A telepítési útmutatót az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben találja.

### <a name="step-12-get-your-azure-subscription-id"></a>1.2 lépés: Az Azure-előfizetés-azonosító beszerezni

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába a következő paranccsal:

```Powershell
   Connect-AzAccount
```
Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Ezután használja a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsot:

```powershell
   Get-AzSubscription
```
A kimeneti, keresse meg az Azure Key Vault hoz használt előfizetés azonosítóját. Erre az előfizetési azonosítóra később lesz szüksége.

Ne zárja be az Azure PowerShell ablakot.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>1.3 lépés: Töltse le a BYOK eszközkészletet az Azure Key Vaulthoz

Nyissa meg a Microsoft letöltőközpontot, és [töltse le az Azure Key Vault BYOK eszközkészletét a](https://www.microsoft.com/download/details.aspx?id=45345) földrajzi régiójához vagy az Azure-példányhoz. A következő információk segítségével azonosíthatja a letöltendő csomag nevét és a hozzá tartozó SHA-256 csomagkivonatot:

---
**Egyesült Államok:**

KeyVault-BYOK-Tools-Egyesült Államok.zip

2E8C003204004301063666A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Európa:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62488888754203721d2F88D300910634A32DFA1FB19E4A

---
**Ázsia:**

KeyVault-BYOK-Eszközök-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latin-Amerika:**

KeyVault-BYOK-Eszközök-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D60C4D03756642F25A538922DD1B01A4FACB619

---
**Japán:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC0651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Eszközök-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDDA05344ED136F

---
**Dél-Afrika:**

KeyVault-BYOK-Tools-Dél-Afrika.zip

C41060C5C0170AAAD896DA732E31433D14C9FC83AC3C6776F46D98620784A

---
**Uae:**

KeyVault-BYOK-Eszközök-UAE.zip

FADE80210B06962A0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Ausztrália:**

KeyVault-BYOK-Tools-Ausztrália.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3E2452A025223EEB16901C40A

---
[**Azure-kormány:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Eszközök-USGovCloud.zip

F8DB2FC914A7360650922391d9A79FF030FD3048B5795EC83ADC59DB018621A

---
**Az Egyesült Államok kormányának dod:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91d1812280207a205442B3DDF861B79B8B9991BB5C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Kanada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C8C2CE249E271934630885797C7B

---
**Németország:**

KeyVault-BYOK-Tools-Németország.zip

5385E615880AFC02AFD9841F7BADD025D7E819894AA29ED3C71C3F844C45D6

---
**Németország Nyilvános:**

KeyVault-BYOK-Tools-Németország-Public.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**India:**

KeyVault-BYOK-Eszközök-India.zip

49EDCEB3091CF1DF7B156d5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Franciaország:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C099F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Egyesült Királyság:**

KeyVault-BYOK-Tools-EgyesültKirályság.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Svájc:**

KeyVault-BYOK-Tools-Svájc.zip

88CF8D39899E26d456E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


A letöltött BYOK-eszközkészlet integritásának ellenőrzéséhez az Azure PowerShell-munkamenetből használja a [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) parancsmag.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Az eszközkészlet a következőket tartalmazza:

* A Key Exchange Key (KEK) csomag, amelynek neve **BYOK-KEK-pkg-kezdetű.**
* A Security World csomag, amelynek neve kezdődik **BYOK-SecurityWorld-pkg-.**
* Egy verifykeypackage.py nevű python **szkript.**
* **KeyTransferRemote.exe** és a kapcsolódó DLLs nevű parancssori végrehajtható fájl.
* Visual C++ újraterjeszthető **csomag, vcredist_x64.exe.**

Másolja a csomagot egy USB-meghajtóra vagy más hordozható tárolóeszközre.

## <a name="step-2-prepare-your-disconnected-workstation"></a>2. lépés: A leválasztott munkaállomás előkészítése

A második lépésben végezze el az alábbi eljárásokat azon a munkaállomáson, amely nem csatlakozik hálózathoz (az internethez vagy a belső hálózathoz).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>2.1 lépés: Készítse elő a leválasztott munkaállomást nCipher nShield HSM-el

Telepítse az nCipher támogatási szoftvert egy Windows rendszerű számítógépre, majd csatlakoztasson egy nCipher nShield HSM-et a számítógéphez.

Győződjön meg arról, hogy az nCipher eszközök az elérési útvonalon vannak (**%nfast_home%\bin**). Adja meg például a következőt:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

További információt az nShield HSM-hez mellékelt használati útmutatóban talál.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>2.2 lépés: Telepítse a BYOK eszközkészletet a leválasztott munkaállomásra

Másolja át a BYOK eszközkészletcsomagot az USB-meghajtóról vagy egyéb hordozható tárolóeszközről, majd tegye a következőket:

1. Csomagolja ki a letöltött csomagban található fájlokat egy tetszőleges mappába.
2. Ebből a könyvtárból futtassa a vcredist_x64.exe fájlt.
3. Kövesse az utasításokat a Visual Studio 2013 Visual C++ futásidejű összetevőinek telepítéséhez.

## <a name="step-3-generate-your-key"></a>3. lépés: A kulcs létrehozása

Ehhez a harmadik lépéshez tegye a következő eljárásokat a leválasztott munkaállomáson. A lépés végrehajtásához a HSM-nek inicializálási módban kell lennie. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>3.1 lépés: A HSM mód módosítása "I" üzemmódra

Ha nCipher nShield Edge-et használ, változtassa meg az üzemmódot: 1. A Mód gombbal jelölje ki a kívánt módot. 2. Néhány másodpercen belül nyomja meg és tartsa lenyomva a Clear gombot néhány másodpercig. Ha az üzemmód megváltozik, az új üzemmód LED-je abbahagyja a villogást és világít. Előfordulhat, hogy az Állapot LED néhány másodpercig szabálytalanul villog, majd rendszeresen villog, amikor a készülék készen áll. Ellenkező esetben a készülék az aktuális üzemmódban marad, a megfelelő üzemmódled világítva.

### <a name="step-32-create-a-security-world"></a>3.2. lépés: Biztonsági világ létrehozása

Indítsa el a parancssort, és futtassa az nCipher új világ programot.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Ez a program létrehoz egy **biztonságivilág**-fájlt az %NFAST_KMDATA%\local\world címen, amely a C:\ProgramData\nCipher\Key Management Data\local mappának felel meg. A kvórumhoz különböző értékeket is használhat, de a mi példánkban három üres kártyát és mindegyikhez egy PIN-kódot kell megadnia. Ezután bármely két kártya teljes hozzáférést biztosít a biztonsági világhoz. Ezek a kártyák lesznek az új biztonsági világ **rendszergazdai kártyakészlete**.

> [!NOTE]
> Ha a HSM nem támogatja az újabb cypher suite DLf3072s256mRijndael, akkor helyettesítheti --cipher-suite= DLf3072s256mRijndael a --cipher-suite=DLf1024s160mRijndael
> 
> Biztonsági világ létre new-world.exe, hogy a hajók nCipher szoftver verzió 12.50 nem kompatibilis ezzel byok eljárás. Két lehetőség érhető el:
> 1) Downgrade nCipher szoftver verzió 12.40.2, hogy hozzon létre egy új biztonsági világot.
> 2) Lépjen kapcsolatba az nCipher ügyfélszolgálatával, és kérje meg őket, hogy adjanak meg egy gyorsjavítást a 12.50-es szoftververzióhoz, amely lehetővé teszi az új világ.exe 12.40.2-es verziójának használatát, amely kompatibilis ezzel a BYOK eljárással.

Ezután tegye a következőket:

* Készítsen biztonsági másolatot a világfájlról. Helyezze biztonságba és biztosítson védelmet a világfájl, valamint a rendszergazdai kártyák és PIN-kódjaik számára, és gondoskodjon róla, hogy senki ne férhessen hozzá egynél több kártyához.

### <a name="step-33-change-the-hsm-mode-to-o"></a>3.3 lépés: A HSM mód módosítása "O" üzemmódra

Ha nCipher nShield Edge-et használ, változtassa meg az üzemmódot: 1. A Mód gombbal jelölje ki a kívánt módot. 2. Néhány másodpercen belül nyomja meg és tartsa lenyomva a Clear gombot néhány másodpercig. Ha az üzemmód megváltozik, az új üzemmód LED-je abbahagyja a villogást és világít. Előfordulhat, hogy az Állapot LED néhány másodpercig szabálytalanul villog, majd rendszeresen villog, amikor a készülék készen áll. Ellenkező esetben a készülék az aktuális üzemmódban marad, a megfelelő üzemmódled világítva.

### <a name="step-34-validate-the-downloaded-package"></a>3.4. lépés: A letöltött csomag ellenőrzése

Ezen lépés végrehajtása nem kötelező, de ajánlott a következők ellenőrzéséhez:

* Az eszközkészletben található kulcscserekulcs egy eredeti nCipher nShield HSM-ből származik.
* A biztonsági világ kivonata, amely szerepel az eszközkészletben, egy valódi nCipher nShield HSM-ben keletkezett.
* A kulcscserekulcs nem exportálható.

> [!NOTE]
> A letöltött csomag ellenőrzéséhez a HSM-nek csatlakoztatva és bekapcsolva kell lennie, és rendelkeznie kell egy biztonsági világgal (például azzal, amelyet Ön épp most hozott létre).

A letöltött csomag ellenőrzése:

1. Futtassa a verifykeypackage.py parancsfájlt az alábbi adatok egyikének beírásával, a földrajzi régiótól vagy az Azure-példánytól függően:

   * Észak-Amerika esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Európa esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Ázsia esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Latin-Amerika esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Japán esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Korea esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Dél-Afrika esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Az Egyesült Arab Emírségek számára:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Ausztrália esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Az [Azure Government](https://azure.microsoft.com/features/gov/), amely az Azure amerikai kormányzati példányát használja:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Az Egyesült Államok kormánya DOD esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Kanada esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Németország esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Németország számára Nyilvános:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * India esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Franciaország esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Egyesült Királyság esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Svájc esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > Az nCipher nShield szoftver tartalmazza a pythont a következő helyen: %NFAST_HOME%\python\bin
     >
     >
2. Ellenőrizze, hogy a következő, amely a sikeres érvényesítést jelzi: **Eredmény: SIKERES**

Ez a parancsfájl érvényesíti az aláíró láncot az nShield gyökérkulcsig. Ennek a gyökérkulcsnak a kivonata be van ágyazva a parancsfájlba, az értékének pedig a következőnek kell lennie: **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Ezt az értéket külön is megerősítheti, ha ellátogat az [nCipher webhelyére.](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation)

Most már készen áll egy új kulcs létrehozására.

### <a name="step-35-create-a-new-key"></a>3.5 lépés: Új kulcs létrehozása

Hozzon létre egy kulcsot a nCipher nShield **generatekey** program segítségével.

A kulcs létrehozásához futtassa a következő parancsot:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

A parancs futtatásakor használja a következő utasításokat:

* A *protect* paramétert a **module** értékre kell állítani, ahogyan az ábrán látható. Ez egy modul által védett kulcsot hoz létre. A BYOK eszközkészlet nem támogatja az OCS által védett kulcsokat.
* Cserélje le a *contosokey* értéket az **ident** és a **plainname** esetében bármilyen tetszőleges sztringre. A felügyeleti költségek minimalizálása és a hibák kockázatának csökkentése érdekében azt javasoljuk, hogy ugyanazt az értéket használja mindkettőhöz. Az **ident** érték csak számokat, kötőjeleket és kisbetűket tartalmazhat.
* A példában a pubexp üresen maradt (alapértelmezett érték), de megadhat specifikus értékeket is. További információt az [nCipher dokumentációjában talál.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Ez a parancs tokenizált kulcsfájlt hoz létre a (%NFAST_KMDATA%\helyi) **mappában, amelynek**neve key_simple_ kezdődik, majd a parancsban megadott **azonosító.** Például: **key_simple_contosokey**. Ez a fájl egy titkosított kulcsot tartalmaz.

Készítsen erről a tokenekre bontott kulcsfájlról egy biztonsági másolatot egy biztonságos helyre.

> [!IMPORTANT]
> Amikor később átviszi a kulcsot az Azure Key Vaultba, a Microsoft nem tudja visszavinni Önnek ezt a kulcsot, ezért rendkívül fontossá válik a kulcs és a biztonsági világ biztonságos biztonsági mentése. Lépjen kapcsolatba [az nCipher-rel](https://www.ncipher.com/about-us/contact-us) a kulcs biztonsági mentéséhez és gyakorlati tanácsaiért.
>


Most már készen áll a kulcs átvitelére az Azure Key Vaultba.

## <a name="step-4-prepare-your-key-for-transfer"></a>4. lépés: A kulcs előkészítése az átvitelre

Ehhez a negyedik lépéshez tegye a következő eljárásokat a leválasztott munkaállomáson.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>4.1. lépés: A kulcs másolatának létrehozása csökkentett engedélyekkel

Nyisson meg egy új parancssort, és módosítsa az aktuális könyvtárat arra a helyre, ahol kicsomagolta a BYOK zip fájlt. A kulcs engedélyeinek csökkentéséhez a parancssorból futtassa az alábbi lehetőségek egyikét a földrajzi régiótól vagy az Azure-példánytól függően:

* Észak-Amerika esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Európa esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Ázsia esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Latin-Amerika esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Japán esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Korea esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Dél-Afrika esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Az Egyesült Arab Emírségek számára:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Ausztrália esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Az [Azure Government](https://azure.microsoft.com/features/gov/), amely az Azure amerikai kormányzati példányát használja:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Az Egyesült Államok kormánya DOD esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Kanada esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Németország esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Németország számára Nyilvános:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* India esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Franciaország esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Egyesült Királyság esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Svájc esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

A parancs futtatásakor cserélje le a *contosokey-t* a **3.5 lépésben** megadott értékre: Hozzon létre egy új kulcsot a Kulcs [létrehozása](#step-3-generate-your-key) lépésből.

A rendszer arra kéri, hogy csatlakoztassa a biztonsági világ admin kártyákat.

Amikor a parancs befejeződik, az **Eredmény: SIKER** és a csökkentett engedélyekkel\<rendelkező kulcs másolata a key_xferacId_ contosokey> nevű fájlban található.

Az ACLS-t a következő parancsokkal vizsgálhatja meg az nCipher nShield segédprogramok segítségével:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  A parancsok futtatásakor cserélje le a contosokey-t a **3.5 lépésben** megadott értékre: Hozzon létre egy új kulcsot a Kulcs [létrehozása](#step-3-generate-your-key) lépésből.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>4.2 lépés: A kulcs titkosítása a Microsoft kulcscserekulcsával

A földrajzi régiótól vagy az Azure-példánytól függően futtassa az alábbi parancsok egyikét:

* Észak-Amerika esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Európa esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Ázsia esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Latin-Amerika esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Japán esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Korea esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dél-Afrika esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Az Egyesült Arab Emírségek számára:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Ausztrália esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Az [Azure Government](https://azure.microsoft.com/features/gov/), amely az Azure amerikai kormányzati példányát használja:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Az Egyesült Államok kormánya DOD esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Kanada esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Németország esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Németország számára Nyilvános:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* India esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Franciaország esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Egyesült Királyság esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Svájc esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


A parancs futtatásakor használja a következő utasításokat:

* Cserélje le a *contosokey-t* a kulcs létrehozásához használt azonosítóra a **3.5 lépésben: Hozzon létre egy új kulcsot** a Kulcs [létrehozása](#step-3-generate-your-key) lépésből.
* Cserélje le *a SubscriptionID-t* a key vaultot tartalmazó Azure-előfizetés azonosítójára. Ezt az értéket korábban, **az 1.2 lépésben szerezte be: Szerezze be az Azure-előfizetés-azonosítóját** [az Internethez csatlakoztatott munkaállomás előkészítése](#step-1-prepare-your-internet-connected-workstation) lépésből.
* Cserélje le *a ContosoFirstHSMKey kulcsot* egy, a kimeneti fájl nevéhez használt címkére.

Ha ez sikeresen befejeződik, az **Eredmény: SIKER** és az aktuális mappában egy új fájl található, amelynek a következő neve van: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>4.3 lépés: A kulcsátviteli csomag másolása az internethez csatlakoztatott munkaállomásra

Usb-meghajtó vagy más hordozható tároló használatával másolja a kimeneti fájlt az előző lépésből (KeyTransferPackage-ContosoFirstHSMkey.byok) az internethez csatlakoztatott munkaállomásra.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>5. lépés: A kulcs átvitele az Azure Key Vaultba

Ehhez az utolsó lépéshez az internethez csatlakoztatott munkaállomáson az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag segítségével töltse fel a leválasztott munkaállomásról az Azure Key Vault HSM-be másolt kulcsátviteli csomagot:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Ha a feltöltés sikeres, megjelenik az imént hozzáadott kulcs tulajdonságai.

## <a name="next-steps"></a>További lépések

Most már használhatja ezt a HSM-védelemmel ellátott kulcsot a key vaultban. További információkért lásd ezt az árat és a szolgáltatás [összehasonlítását](https://azure.microsoft.com/pricing/details/key-vault/).
