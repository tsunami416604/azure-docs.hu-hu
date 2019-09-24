---
title: HSM-védelemmel ellátott kulcsok generálása és átvitele a Azure Key Vault-Azure Key Vaulthoz | Microsoft Docs
description: Ez a cikk segítséget nyújt a saját HSM-védelemmel ellátott kulcsok tervezéséhez, létrehozásához és átviteléhez a Azure Key Vault használatával. Más néven BYOK vagy saját kulcs használata.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 71b7e4bd9406e7fb300ebccd86908820b7628c29
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71000769"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>HSM-védelemmel ellátott kulcsok generálása és átvitele Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha további garanciára van szüksége, amikor Azure Key Vault használ, a hardveres biztonsági modulokban (HSM) olyan kulcsokat importálhat vagy generálhat, amelyek soha nem hagyják el a HSM-határt. Ezt a forgatókönyvet gyakran a *saját kulcs*használatára vagy BYOK kell hivatkozni. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. A Azure Key Vault a HSM nCipher nShield-családját használja a kulcsok védeleméhez.

A témakörben található információk segítségével megtervezheti, létrehozhatja és átviheti a saját HSM-védelemmel ellátott kulcsait, hogy azok a Azure Key Vault használatával legyenek használatban.

Ez a funkció nem érhető el az Azure China-ban.

> [!NOTE]
> További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](key-vault-overview.md)  
> Az első lépéseket ismertető oktatóanyaghoz, amely magában foglalja a HSM-védelemmel ellátott kulcsok kulcstartójának létrehozását, lásd: [Mi az Azure Key Vault?](key-vault-overview.md)

További információ a HSM-védelemmel ellátott kulcsok interneten keresztül történő létrehozásáról és átadásáról:

* A kulcsot egy kapcsolat nélküli munkaállomáson hozza meg, amely csökkenti a támadási felületet.
* A kulcs titkosítva van egy Key Exchange-kulccsal (KEK), amely titkosítva marad, amíg át nem kerül a Azure Key Vault HSM. Csak a kulcs titkosított verziója hagyja az eredeti munkaállomást.
* Az eszközkészlet beállítja a bérlői kulcs azon tulajdonságait, amelyek a kulcsot a Azure Key Vault biztonsági világhoz kötik. Így miután a Azure Key Vault HSM megkapja és visszafejti a kulcsot, csak ezek a HSM használhatják. A kulcs nem exportálható. Ezt a kötést a nCipher-HSM kényszeríti ki.
* A kulcs titkosításához használt kulcscsere-kulcs (KEK) a Azure Key Vault HSM belül jön létre, és nem exportálható. A HSM azt kényszeríti, hogy nem lehet a KEK tiszta verziója a HSM-en kívül. Emellett az eszközkészlet olyan igazolást is tartalmaz, amely a nCipher, hogy a KEK nem exportálható, és a nCipher által gyártott eredeti HSM-ben jött létre.
* Az eszközkészlet magában foglalja a nCipher igazolását, hogy a Azure Key Vault biztonsági világot a nCipher által gyártott eredeti HSM is létrehozta. Ez a tanúsítvány igazolja, hogy a Microsoft valódi hardvert használ.
* A Microsoft külön KEK és külön biztonsági világok használatát használja az egyes földrajzi régiókban. Ez a szétválasztás biztosítja, hogy a kulcsot csak abban a régióban lévő adatközpontokban lehessen használni, amelyben a titkosítást elvégezte. Egy európai ügyfél kulcsa például nem használható Észak-amerikai vagy ázsiai adatközpontokban.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>További információ a nCipher HSM és a Microsoft szolgáltatásairól

a nCipher Security az adattitkosítási és a számítógépes biztonsági megoldások vezető globális szolgáltatója a pénzügyi szolgáltatások, a csúcstechnológiai, a gyártási, a kormányzati és a technológiai szektorok számára. A vállalati és kormányzati információk védelme 40 éves nCipher a biztonsági titkosítási megoldásokat az öt legnagyobb energia-és repülőgépipari vállalat használja. A megoldásaikat 22 NATO-ország/-régió is használja, és a világszerte több mint 80%-os fizetési tranzakciót biztosít.

A Microsoft a nCipher biztonsággal együttműködve fejleszti a HSM. Ezek a fejlesztések lehetővé teszik az üzemeltetett szolgáltatások tipikus előnyeinek beszerzését anélkül, hogy a kulcsok feletti irányítást kellene lemondani. Pontosabban ezek a fejlesztések lehetővé teszik a Microsoft számára a HSM kezelését, hogy ne kelljen. Felhőalapú szolgáltatásként a Azure Key Vault rövid időn belül átméretezi a szervezet használati feladatainak teljesítését. Ugyanakkor a kulcsot a Microsoft HSM védi: Megtarthatja a kulcs életciklusának szabályozását, mivel létrehozza a kulcsot, és továbbítja azt a Microsoft HSM.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Saját kulcs (BYOK) implementálása a Azure Key Vault

Ha létrehoz egy saját HSM-védelemmel ellátott kulcsot a következő információk és eljárások segítségével, majd áthelyezi azt a Azure Key Vaultba – a saját kulcs használata (BYOK) forgatókönyv.

## <a name="prerequisites-for-byok"></a>A BYOK előfeltételei

Az alábbi táblázatban megtekintheti a saját kulcs (BYOK) használatának előfeltételeit a Azure Key Vaulthoz.

| Követelmény | További információ |
| --- | --- |
| Előfizetés az Azure-ba |Azure Key Vault létrehozásához Azure-előfizetésre van szükség: [Regisztráljon az ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/) |
| A Azure Key Vault Premium szolgáltatási réteg HSM-védelemmel ellátott kulcsok támogatásához |A Azure Key Vault szolgáltatási szintjeivel és képességeivel kapcsolatos további információkért tekintse meg a [Azure Key Vault díjszabási](https://azure.microsoft.com/pricing/details/key-vault/) webhelyét. |
| nCipher nShield HSM, intelligens kártyák és támogatási szoftverek |Hozzá kell férnie a nCipher hardveres biztonsági modulhoz, valamint a nCipher nShield HSM alapszintű működési ismeretéhez. Tekintse meg a [NCipher NShield hardveres biztonsági modult](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) a kompatibilis modellek listájához, vagy ha nem rendelkezik ilyennel, akkor VÁSÁROLjon HSM-et. |
| A következő hardverek és szoftverek:<ol><li>Egy offline x64-es munkaállomás, amely a Windows 7 minimális Windows-operációs rendszerét és a nCipher nShield szoftvert legalább 11,50-es verzióval rendelkezik.<br/><br/>Ha ez a munkaállomás Windows 7 rendszert futtat, [telepítenie kell Microsoft .NET Framework 4,5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)-es keretrendszert.</li><li>Egy internethez csatlakozó munkaállomás, amely a Windows 7 minimális Windows operációs rendszerét és a [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **minimálisan szükséges 1.1.0-verziót** telepítette.</li><li>USB-meghajtó vagy más hordozható tárolóeszköz, amelyen legalább 16 MB szabad terület található.</li></ol> |Biztonsági okokból javasoljuk, hogy az első munkaállomás ne kapcsolódjon a hálózathoz. Ez a javaslat azonban nincs programozott módon kényszerítve.<br/><br/>Az alábbi utasításokban ezt a munkaállomást leválasztott munkaállomásként nevezzük.</p></blockquote><br/>Emellett, ha a bérlői kulcs éles hálózatra vonatkozik, javasoljuk, hogy egy második, külön munkaállomás használatával töltse le az eszközkészletet, és töltse fel a bérlői kulcsot. Tesztelési célokra azonban használhatja ugyanazt a munkaállomást, mint az elsőt.<br/><br/>Az alábbi utasításokban ezt a második munkaállomást internetkapcsolattal rendelkező munkaállomásnak nevezzük.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>A kulcs előállítása és átvitele Azure Key Vault HSM-be

A következő öt lépést fogja használni a kulcs egy Azure Key Vault HSM-ben történő létrehozásához és átviteléhez:

* [1. lépés: Az internetkapcsolattal rendelkező munkaállomás előkészítése](#step-1-prepare-your-internet-connected-workstation)
* [2. lépés: A leválasztott munkaállomás előkészítése](#step-2-prepare-your-disconnected-workstation)
* [3. lépés: A kulcs előállítása](#step-3-generate-your-key)
* [4. lépés: A kulcs előkészítése az átvitelhez](#step-4-prepare-your-key-for-transfer)
* [5. lépés: A kulcs átvitele Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>1\. lépés: Az internetkapcsolattal rendelkező munkaállomás előkészítése

Ebben az első lépésben hajtsa végre az alábbi eljárásokat az internethez csatlakozó munkaállomáson.

### <a name="step-11-install-azure-powershell"></a>1,1. lépés: Az Azure PowerShell telepítése

Az internethez csatlakoztatott munkaállomáson töltse le és telepítse a Azure PowerShell modult, amely tartalmazza a Azure Key Vault kezelésére szolgáló parancsmagokat. A telepítési utasításokért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>1,2. lépés: Azure-előfizetés AZONOSÍTÓjának beolvasása

Indítsa el az Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába a következő parancs használatával:

```Powershell
   Connect-AzAccount
```
Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Ezután használja a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsot:

```powershell
   Get-AzSubscription
```
A kimenetben keresse meg a Azure Key Vaulthoz használni kívánt előfizetés AZONOSÍTÓját. Később szüksége lesz erre az előfizetés-AZONOSÍTÓra.

Ne zárjuk be a Azure PowerShell ablakot.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>1,3. lépés: A Azure Key Vault BYOK eszközkészletének letöltése

Nyissa meg a Microsoft letöltőközpontból, és [töltse le a Azure Key Vault BYOK eszközkészletet](https://www.microsoft.com/download/details.aspx?id=45345) az Azure földrajzi régiójához vagy példányához. Az alábbi információk segítségével azonosíthatja a letöltött csomag nevét és a hozzá tartozó SHA-256 csomag kivonatát:

---
**Egyesült Államok:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Európa**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Ázsia**

KeyVault-BYOK-Tools-AsiaPacific. zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latin-Amerika:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japán:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Dél-Afrika:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**EGYESÜLT ARAB EMÍRSÉGEK**

KeyVault-BYOK-Tools-UAE. zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Ausztrália:**

KeyVault-BYOK-Tools-Australia. zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Government:** ](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**USA-beli kormányzati DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Németország:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Németország nyilvános:**

KeyVault-BYOK-Tools-Germany-Public. zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**India:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Franciaország:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Egyesült Királyság:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Svájc:**

KeyVault-BYOK-Tools-Switzerland. zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


A letöltött BYOK-eszközkészlet integritásának ellenőrzéséhez a Azure PowerShell-munkamenetben használja a [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) parancsmagot.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Az eszközkészlet az alábbiakat tartalmazza:

* Egy Key Exchange Key (KEK) csomag, amelynek a neve **BYOK-KEK-pkg--** vel kezdődik.
* Egy biztonsági globális csomag, amelynek a neve **BYOK-SecurityWorld-pkg-** vel kezdődik.
* Egy verifykeypackage.py nevű Python-szkript **.**
* Egy **KeyTransferRemote. exe** nevű parancssori végrehajtható fájl és a hozzá tartozó DLL-fájlok.
* Egy C++ **vcredist_x64. exe** nevű vizualizációs Újraterjeszthető csomag.

Másolja a csomagot egy USB-meghajtóra vagy más hordozható tárolóba.

## <a name="step-2-prepare-your-disconnected-workstation"></a>2\. lépés: A leválasztott munkaállomás előkészítése

Ebben a második lépésben hajtsa végre az alábbi eljárásokat azon a munkaállomáson, amely nem csatlakozik hálózathoz (az internethez vagy a belső hálózathoz).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>2,1. lépés: A leválasztott munkaállomás előkészítése a nCipher nShield HSM-sel

Telepítse a nCipher támogatási szoftverét egy Windows rendszerű számítógépre, majd csatoljon egy nCipher nShield HSM-t a számítógéphez.

Győződjön meg arról, hogy a nCipher-eszközök az elérési úton vannak ( **%nfast_home%\Bin**). Írja be például a következőt:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

További információkért tekintse meg a nShield HSM-hez mellékelt felhasználói útmutatót.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>2,2. lépés: A BYOK eszközkészlet telepítése a kapcsolat nélküli munkaállomásra

Másolja a BYOK eszközkészlet csomagot az USB-meghajtóról vagy más hordozható tárolóból, majd tegye a következőket:

1. Bontsa ki a fájlokat a letöltött csomagból bármely mappába.
2. A mappában futtassa a vcredist_x64. exe fájlt.
3. Kövesse a Visual Studio 2013-es verziójának Visual C++ Runtime-összetevőinek telepítése című témakör utasításait.

## <a name="step-3-generate-your-key"></a>3\. lépés: A kulcs előállítása

Ehhez a harmadik lépéshez hajtsa végre az alábbi eljárásokat a leválasztott munkaállomáson. A lépés elvégzéséhez a HSM inicializálási módban kell lennie. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>3,1. lépés: A HSM üzemmód módosítása az "I" értékre

Ha a nCipher nShield Edge-t használja, módosítsa a módot: 1. A Mode (mód) gomb használatával kiemelheti a szükséges módot. 2. Néhány másodpercen belül nyomja le és tartsa nyomva a Clear (Törlés) gombot néhány másodpercig. Ha a mód megváltozik, az új mód LED-je leáll, és megvilágítva marad. Előfordulhat, hogy az állapot LED-je szabálytalanul villan fel néhány másodpercig, majd rendszeresen villog, amikor az eszköz készen áll. Ellenkező esetben az eszköz az aktuális módban marad, és a megfelelő mód LED világít.

### <a name="step-32-create-a-security-world"></a>3,2. lépés: Biztonsági világ létrehozása

Indítsa el a parancssort, és futtassa a nCipher New-World programot.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Ez a program létrehoz egy **biztonsági globális** fájlt a%NFAST_KMDATA%\local\world címen, amely megfelel a C:\ProgramData\nCipher\Key felügyeleti Settings\User mappájának. A kvórumhoz különböző értékeket használhat, de a példában a rendszer három üres kártyát és PIN-kód megadását kéri. Ezután bármelyik két kártya teljes hozzáférést biztosít a biztonsági világhoz. Ezek a kártyák lesznek az új biztonsági világhoz **beállított rendszergazdai kártya** .

> [!NOTE]
> Ha a HSM nem támogatja az újabb Cypher Suite-DLf3072s256mRijndael, cserélje le a--Cipher-Suite = DLf3072s256mRijndael with--Cipher-Suite = DLf1024s160mRijndael

Ezután tegye a következőket:

* A globális fájl biztonsági mentése. Gondoskodjon a globális fájl, a rendszergazdai kártyák és a PIN-kód védelméről, és győződjön meg arról, hogy egyetlen személy sem fér hozzá egynél több kártyához.

### <a name="step-33-change-the-hsm-mode-to-o"></a>3,3. lépés: A HSM üzemmód módosítása az "O" értékre

Ha a nCipher nShield Edge-t használja, módosítsa a módot: 1. A Mode (mód) gomb használatával kiemelheti a szükséges módot. 2. Néhány másodpercen belül nyomja le és tartsa nyomva a Clear (Törlés) gombot néhány másodpercig. Ha a mód megváltozik, az új mód LED-je leáll, és megvilágítva marad. Előfordulhat, hogy az állapot LED-je szabálytalanul villan fel néhány másodpercig, majd rendszeresen villog, amikor az eszköz készen áll. Ellenkező esetben az eszköz az aktuális módban marad, és a megfelelő mód LED világít.

### <a name="step-34-validate-the-downloaded-package"></a>3,4. lépés: A letöltött csomag ellenőrzése

Ez a lépés nem kötelező, de ajánlott, hogy ellenőrizni tudja a következőket:

* Az eszközkészletben található kulcscsere-kulcs egy valódi nCipher-nShield HSM-ből lett létrehozva.
* Az eszközkészletben található biztonsági világ kivonata egy valódi nCipher-nShield HSM-ben lett létrehozva.
* A kulcscsere kulcsa nem exportálható.

> [!NOTE]
> A letöltött csomag érvényesítéséhez a HSM-nek csatlakoztatva kell lennie, be kell kapcsolnia, és rendelkeznie kell egy biztonsági világban (például az imént létrehozott).

A letöltött csomag ellenőrzése:

1. Futtassa a verifykeypackage.py szkriptet a következők egyikének beírásával az Azure földrajzi régiójától vagy-példánytól függően:

   * Észak-Amerika esetén:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Európa esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Ázsia esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Latin-Amerika esetén:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Japán esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Korea esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Dél-Afrika esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * UAE esetén:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Ausztráliában:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * [Azure Government](https://azure.microsoft.com/features/gov/), amely az Azure Egyesült államokbeli kormányzati példányát használja:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * USA-beli kormányzati DOD esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Kanada esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Németország esetében:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Németország számára nyilvános:

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
     > A nCipher nShield szoftver tartalmazza a Pythont a következő címen:%NFAST_HOME%\python\bin
     >
     >
2. Győződjön meg arról, hogy a következő jelenik meg, amely a sikeres ellenőrzést jelzi: **Találat SIKERES**

Ez a szkript érvényesíti az aláíró láncot a nShield legfelső szintű kulcsával. A legfelső szintű kulcs kivonata be van ágyazva a szkriptbe, és annak értéke **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**kell, hogy legyen. Ezt az értéket külön is megerősítheti, ha felkeresi a [nCipher webhelyét](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

Most már készen áll egy új kulcs létrehozására.

### <a name="step-35-create-a-new-key"></a>3,5. lépés: Új kulcs létrehozása

Kulcs létrehozása a nCipher nShield **Kulcslétrehozási programjával** program használatával.

Futtassa a következő parancsot a kulcs létrehozásához:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

A parancs futtatásakor kövesse az alábbi utasításokat:

* A *Protect* paramétert az érték **modulra**kell beállítani az ábrán látható módon. Ez létrehoz egy modul által védett kulcsot. A BYOK eszközkészlet nem támogatja a OCS által védett kulcsokat.
* Cserélje le az *contosokey értéket ugyanarra* értékét az **ident** és a **plainname** értékre bármilyen karakterlánccal. A rendszergazdai felügyelet csökkentése és a hibák kockázatának csökkentése érdekében javasoljuk, hogy mindkét értéknél ugyanazt az értéket használja. Az **ident** érték csak számokat, kötőjeleket és kisbetűket tartalmazhat.
* Ebben a példában a pubexp üresen marad (alapértelmezett), de konkrét értékeket is megadhat. További információkért tekintse meg a [nCipher dokumentációját.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Ez a parancs létrehoz egy jogkivonat-kulcsot a%NFAST_KMDATA%\local mappában, amelynek a neve **key_simple_** , majd a parancsban megadott **ident** . Például: **key_simple_contosokey**. Ez a fájl titkosított kulcsot tartalmaz.

Biztonsági másolat készítése a jogkivonat-kulcsfájl biztonságos helyen.

> [!IMPORTANT]
> Amikor később áthelyezi a kulcsot Azure Key Vaultre, a Microsoft nem tudja visszaexportálni ezt a kulcsot, így rendkívül fontos, hogy biztonságosan biztonsági másolatot készíthet a kulcsról és a biztonsági világról. A kulcs biztonsági mentésével kapcsolatos útmutatásért és ajánlott eljárásért forduljon a [nCipher](https://www.ncipher.com/about-us/contact-us) .
>


Most már készen áll arra, hogy átvigye a kulcsot Azure Key Vaultba.

## <a name="step-4-prepare-your-key-for-transfer"></a>4\. lépés: A kulcs előkészítése az átvitelhez

Ehhez a negyedik lépéshez hajtsa végre az alábbi eljárásokat a leválasztott munkaállomáson.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>4,1. lépés: A kulcs másolatának létrehozása a korlátozott engedélyekkel

Nyisson meg egy új parancssort, és módosítsa az aktuális könyvtárat arra a helyre, ahová kibontja a BYOK zip-fájlját. A kulcs engedélyeinek csökkentése érdekében a parancssorból futtassa a következők egyikét, az Azure földrajzi régiójától vagy az Azure-példánytól függően:

* Észak-Amerika esetén:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Európa esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Ázsia esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Latin-Amerika esetén:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Japán esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Korea esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Dél-Afrika esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* UAE esetén:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Ausztráliában:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* [Azure Government](https://azure.microsoft.com/features/gov/), amely az Azure Egyesült államokbeli kormányzati példányát használja:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* USA-beli kormányzati DOD esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Kanada esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Németország esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Németország számára nyilvános:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* India esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Franciaország esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Egyesült Királyság esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Svájc esetében:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

A parancs futtatásakor cserélje le a *contosokey értéket ugyanarra* értéket ugyanazzal az értékkel, amelyet **a 3,5-es lépésben megadott: Hozzon létre egy** új kulcsot a [kulcs létrehozása](#step-3-generate-your-key) lépésből.

A rendszer megkéri, hogy csatlakoztassa a biztonsági világ rendszergazdai kártyáit.

A parancs befejeződése után a következő eredményt látja **: A** sikeres és a korlátozott engedélyekkel rendelkező kulcs másolata a key_xferacId_\<contosokey értéket ugyanarra > nevű fájlban található.

Az ACL-EKET a következő parancsokkal ellenőrizheti a nCipher nShield segédprogramok használatával:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump. exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  A parancsok futtatásakor cserélje le a contosokey értéket ugyanarra értéket ugyanazzal az értékkel, amelyet **a 3,5-es lépésben adott meg: Hozzon létre egy** új kulcsot a [kulcs létrehozása](#step-3-generate-your-key) lépésből.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>4,2. lépés: A kulcs titkosítása a Microsoft kulcscsere-kulcsának használatával

Futtassa a következő parancsok egyikét az Azure földrajzi régiójától vagy-példánytól függően:

* Észak-Amerika esetén:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Európa esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Ázsia esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Latin-Amerika esetén:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Japán esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Korea esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dél-Afrika esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* UAE esetén:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Ausztráliában:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* [Azure Government](https://azure.microsoft.com/features/gov/), amely az Azure Egyesült államokbeli kormányzati példányát használja:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* USA-beli kormányzati DOD esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Kanada esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Németország esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Németország számára nyilvános:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* India esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Franciaország esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Egyesült Királyság esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Svájc esetében:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


A parancs futtatásakor kövesse az alábbi utasításokat:

* Cserélje le az *contosokey értéket ugyanarra* -t arra az azonosítóra, amelyet a **3,5-es lépésben a kulcs létrehozásához használt. Hozzon létre egy** új kulcsot a [kulcs létrehozása](#step-3-generate-your-key) lépésből.
* Cserélje le a *SubscriptionID* -t a Key vaultot tartalmazó Azure-előfizetés azonosítójával. Ezt az értéket korábban a **1,2. lépésben lekérte: Szerezze be az Azure-** előfizetés azonosítóját az [internethez csatlakoztatott munkaállomás előkészítése](#step-1-prepare-your-internet-connected-workstation) lépésből.
* Cserélje le a *ContosoFirstHSMKey* a kimeneti fájl nevéhez használt címkére.

Ha a művelet sikeresen befejeződött, a következő **eredményt jeleníti meg: Sikeres** , és az aktuális mappában van egy új fájl, amelynek neve a következő: KeyTransferPackage-*ContosoFirstHSMkey*. byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>4,3. lépés: A kulcs átvitelére szolgáló csomag másolása az internethez csatlakoztatott munkaállomásra

USB-meghajtó vagy más hordozható tároló használatával másolja a kimeneti fájlt az előző lépésből (KeyTransferPackage-ContosoFirstHSMkey. byok) az internethez csatlakoztatott munkaállomásra.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>5\. lépés: A kulcs átvitele Azure Key Vault

Ebben az utolsó lépésben az internethez csatlakoztatott munkaállomáson használja az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmagot a leválasztott munkaállomásról a Azure Key Vault HSM-be másolt kulcs-átadási csomag feltöltéséhez:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Ha a feltöltés sikeres, megjelenik az imént hozzáadott kulcs tulajdonságai.

## <a name="next-steps"></a>További lépések

Ezt a HSM-védelemmel ellátott kulcsot már használhatja a kulcstartóban. További információkért tekintse meg ezt az árat és a szolgáltatás [összehasonlítását](https://azure.microsoft.com/pricing/details/key-vault/).
