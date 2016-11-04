---
title: Bevezetés az Azure Key Vault használatába | Microsoft Docs
description: Ez az oktatóanyag segítségére lesz az Azure Key Vault szolgáltatás megismerésében, amellyel megerősített tárolókat hozhat létre, valamint kriptográfiai kulcsokat és titkos kódokat tárolhat az Azure-ban.
services: key-vault
documentationcenter: ''
author: cabailey
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/10/2016
ms.author: cabailey

---
# Bevezetés az Azure Key Vault használatába
Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

## Bevezetés
Ez az oktatóanyag segít megismerni az Azure Key Vault szolgáltatást, amellyel megerősített tárolókat (kulcstartókat) hozhat létre, valamint kriptográfiai kulcsokat és titkos kódokat tárolhat az Azure-ban. A cikk ismerteti az Azure-alkalmazásokkal kompatibilis kulcsokat vagy jelszavakat tartalmazó tárolók létrehozását az Azure PowerShellben. Ezután bemutatja, hogyan használhatják az adott kulcsot vagy jelszót az alkalmazásai.

**Az oktatóanyag áttekintésének várható időtartama:** 20 perc

> [!NOTE]
> Ez az oktatóanyag nem tartalmaz arra vonatkozó útmutatást, hogy hogyan kell létrehozni a kulcstartó valamely kulcsa vagy titkos kódja használatának az alkalmazások számára való engedélyezésével foglalkozó lépésben szereplő Azure-alkalmazást.
> 
> Az oktatóanyag az Azure PowerShellt használja. A platformfüggetlen parancssori felületre vonatkozó utasításokat [ebben az oktatóanyagban](key-vault-manage-with-cli.md) tekintheti meg.
> 
> 

Áttekintést az Azure Key Vaultról a [What is Azure Key Vault?](key-vault-whatis.md) (Mi az Azure Key Vault?) című cikkben talál.

## Előfeltételek
Az oktatóanyag teljesítéséhez szüksége lesz:

* Egy Microsoft Azure-előfizetésre. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
* Az Azure PowerShell legalább **1.1.0-ás verziójára**. Az Azure PowerShell telepítésérről és az Azure-előfizetéssel való társításáról további információt [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat. Ha már telepítette az Azure PowerShellt, de nem tudja, melyik verziót, írja be az Azure PowerShell-konzolon az alábbi parancsot: `(Get-Module azure -ListAvailable).Version`. Ha az Azure PowerShell 0.9.1-től 0.9.8-ig terjedő verziói közül rendelkezik valamelyikkel, néhány apró eltéréstől függetlenül Önre is vonatkozik az útmutató. Például a `Switch-AzureMode AzureResourceManager` parancsot kell használnia, valamint bizonyos Azure Key Vault parancsok módosultak. A 0.9.1 és 0.9.8 közötti verziók Key Vault parancsmagjainak listája az alábbi témakörben található: [Azure Key Vault Cmdlets](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx) (Az Azure Key Vault parancsmagjai). 
* Egy, az útmutató során létrehozott kulcs vagy jelszó használatához konfigurált alkalmazásra. Egy mintaalkalmazás elérhető a [Microsoft letöltőközpontból](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Útmutatásért tekintse meg a kísérő információs fájlt.

Ez az útmutató kezdő Azure PowerShell-felhasználók számára készült, de a követéséhez tisztában kell lennie az olyan alapszintű fogalmakkal, mint a modulok, a parancsmagok és a munkamenetek. További információ: [Getting started with Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx) (Ismerkedés a Windows PowerShellel).

Az útmutatóban található parancsmagokhoz részletes segítséget kérhet a **Get-Help** parancsmaggal.

    Get-Help <cmdlet-name> -Detailed

Például ha a **Login-AzureRmAccount** parancsmaghoz szeretne segítséget kérni, írja be a következőt:

    Get-Help Login-AzureRmAccount -Detailed

Az alábbi útmutatókkal megismerkedhet az Azure Resource Manager az Azure PowerShellel való használatával:

* [How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)](../powershell-install-configure.md)
* [Using Azure PowerShell with Resource Manager (Az Azure PowerShell és a Resource Manager együttes használata)](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Csatlakozás az előfizetésekhez
Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

    Login-AzureRmAccount 

Vegye figyelembe, hogy az Azure bizonyos példányainak, például az Azure Governmentnek a használatakor az -Environment paramétert kell használni ehhez a parancshoz. Példa: `Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell megkeresi az összes olyan előfizetést, amely ehhez a fiókhoz van rendelve, és alapértelmezés szerint kiválasztja az elsőt.

Ha több előfizetése van, és meg szeretné szabni, hogy melyiket használja az Azure Key Vaulthoz, írja be az alábbi parancsot a fiókhoz tartozó előfizetések megtekintéséhez:

    Get-AzureRmSubscription

Ezt követően írja be az alábbi parancsot a kívánt előfizetés kiválasztásához:

    Set-AzureRmContext -SubscriptionId <subscription ID>

További információ az Azure PowerShell konfigurálásáról: [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása).

## <a id="resource"></a>Új erőforráscsoport létrehozása
Az Azure Resource Manager használatakor minden kapcsolódó erőforrás egy erőforráscsoportban jön létre. Ehhez az útmutatóhoz hozzon létre egy új erőforráscsoportot **ContosoResourceGroup** névvel:

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Kulcstartó létrehozása
A[New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) parancsmag segítségével hozzon létre egy kulcstartót. Ez a parancsmag három kötelező paraméterrel rendelkezik: egy **erőforráscsoport-név**, egy **kulcstartónév**, és a **földrajzi hely**.

Ha például a tároló neve **ContosoKeyVault**, az erőforráscsoport neve **ContosoResourceGroup**, a földrajzi hely pedig **Kelet-Ázsia**, írja be az alábbi parancsot:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott kulcstartó tulajdonságait. A két legfontosabb tulajdonság:

* **A tároló neve**: A példában ez a **ContosoKeyVault**. Ezt a nevet fogja majd más Key Vault parancsmagokban is megadni.
* **A tároló URI-ja**: A példában ez a https://contosokeyvault.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Azure-fiókja most már engedéllyel rendelkezik arra, hogy bármilyen műveletet végezzen ezen a kulcstartón. Egyelőre senki másnak nincs erre engedélye.

> [!NOTE]
> Ha a **Az előfizetés nincs regisztrálva a(z) Microsoft.KeyVault névtér használatára** hibaüzenettel találkozik egy új kulcstartó létrehozásakor, futtassa a `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` parancsot, majd futtassa újra az New-AzureRmKeyVault parancsot. További információ: [Register-AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx).
> 
> 

## <a id="add"></a>Kulcs vagy titkos kód hozzáadása a kulcstartóhoz
Ha azt szeretné, hogy az Azure Key Vault létrehozzon egy szoftveresen védett kulcsot, használja az [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) parancsmagot, majd írja be a következő parancsot:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Azonban ha már rendelkezik egy C:\ meghajtóra mentett, softkey.pfx nevű .PFX-fájlban tárolt kulccsal, azt is feltöltheti az Azure Key Vaultba. Ehhez először írja be az alábbi parancsot a .PFX-fájl **securepfxpwd** változójának **123** értékre állításához:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Ezután az alábbi paranccsal importálja a kulcsot a .PFX-fájlból, így szoftveres védelmet biztosíthat neki a Key Vault szolgáltatásban:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


A létrehozott vagy az Azure Key Vaultba feltöltött kulcsra ez után az URI használatával hivatkozhat. A **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** oldalról mindig letöltheti a legfrissebb verziót, a **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** oldalról pedig ezt a verziót töltheti le.  

A kulcs URI-jának megjelenítéséhez írja be az alábbi parancsot:

    $Key.key.kid

A titkos kód – amely egy SQLPassword nevű, Pa$$w0rd értékű jelszó –, hozzáadásához az Azure Key Vault szolgáltatásbeli tárolóhoz először konvertálja Pa$$w0rdről egy biztonságos karakterláncra az értékét az alábbi paranccsal:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Ezután írja be a következő parancsot:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Az Azure Key Vaulthoz hozzáadott jelszóra ez után az URI használatával hivatkozhat. A **https://ContosoVault.vault.azure.net/secrets/SQLPassword** oldalról mindig letöltheti a legfrissebb verziót, a **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** oldalról pedig ezt a verziót töltheti le.

A titkos kódok URI-jának megjelenítéséhez írja be az alábbi parancsot:

    $secret.Id

Tekintse meg az előbb létrehozott kulcsot vagy titkos kódot:

* A kulcs megtekintéséhez írja be az alábbi parancsot: `Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
* A titkos kód megtekintéséhez írja be az alábbi parancsot: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

A kulcstartó és a kulcs vagy a titkos kód mostantól készen áll az alkalmazásokkal való használatra. Használatukat engedélyeznie kell az alkalmazások számára.  

## <a id="register"></a>Alkalmazás regisztrálása az Azure Active Directory szolgáltatásban
Ezt a lépést általában egy fejlesztő végzi egy másik számítógépről. Nem kifejezetten az Azure Key Vaulthoz kapcsolódik, de a teljes körű tájékoztatás érdekében bemutatjuk.

> [!IMPORTANT]
> Az oktatóprogram sikeres befejezéséhez a fiókjának, a tárolónak és az ebben a lépésben regisztrálandó alkalmazásnak ugyanabban az Azure-címtárban kell lenniük.
> 
> 

A kulcstartót használó alkalmazásoknak az Azure Active Directoryból származó jogkivonat használatával kell hitelesítést végezniük. Ehhez az alkalmazás tulajdonosának először regisztrálnia kell az alkalmazást az Azure Active Directory szolgáltatásban. A regisztrációt követően az alkalmazás tulajdonosa az alábbi értékeket kapja:

* Egy **alkalmazásazonosítót** (más néven ügyfélazonosítót) és egy **hitelesítési kulcsot** (más néven közös titkos kódot). Az alkalmazásnak mindkét értékkel rendelkeznie kell ahhoz, hogy Azure Active Directory-jogkivonatot kapjon. Az értékek megadásának módja az adott alkalmazástól függ. A Key Vault mintaalkalmazás esetében az alkalmazás tulajdonosa adja meg ezeket az értékeket az app.config fájlban.

Az alkalmazás regisztrálása az Azure Active Directory szolgáltatásban:

1. Jelentkezzen be a klasszikus Azure portálra.
2. Kattintson a bal oldalon az **Active Directory** elemre, majd válassza ki azt a címtárat, amelyben regisztrálni kívánja az alkalmazást. <br> <br> **Megjegyzés:** Azt a címtárat kell kiválasztania, amely a kulcstartót létrehozó Azure-előfizetést tartalmazza. Ha nem tudja, hogy melyik ez a címtár, kattintson a **Settings** (Beállítások) lehetőségre, keresse meg a kulcstartót létrehozó előfizetést, és jegyezze fel az utolsó oszlopban megjelenített nevét.
3. Kattintson az **APPLICATIONS** (ALKALMAZÁSOK) elemre. Ha még nem adott hozzá alkalmazást a címtárhoz, ezen az oldalon csak az **Add an App** (Alkalmazás hozzáadása) hivatkozás jelenik meg. Kattintson a hivatkozásra, vagy kattintson a **ADD** (HOZZÁADÁS) gombra a parancssávban.
4. Az **ADD APPLICATION** (ALKALMAZÁS HOZZÁADÁSA) varázsló **What do you want to do?** (Mit kíván tenni?) lapján kattintson a **Add an application my organization is developing** (A vállalatom által fejlesztett alkalmazás hozzáadása) lehetőségre.
5. A **Tell us about your application** (Az alkalmazás bemutatása) oldalon adjon meg egy nevet az alkalmazásnak, majd válassza a **WEB APPLICATION AND/OR WEB API** (WEBALKALMAZÁS ÉS/VAGY WEBES API) (az alapértelmezett beállítás) lehetőséget. Kattintson a **Next** (Tovább) ikonra.
6. Az **App properties** (Alkalmazás tulajdonságai) oldalon adja meg a webalkalmazás bejelentkezési URL-címét és az alkalmazásazonosító URI-ját a **SIGN-ON URL**, illetve az **APP ID URI** mezőkben. Ha az alkalmazás nem rendelkezik ezekkel az értékekkel, ehhez a lépéshez nem létező értékeket is megadhat (például http://test1.contoso.com mindkét mezőhöz). Nem számít, hogy ezek a webhelyek léteznek-e. Az a fontos, hogy az egyes alkalmazások alkalmazásazonosítójának URI-ja a címtár minden alkalmazásánál más legyen. A címtár ezzel a karakterlánccal azonosítja az alkalmazást.
7. Kattintson a **Complete** (Befejezés) ikonra a varázslóban a módosítások mentéséhez.
8. A **Quick Start** (Első lépések) lapon kattintson a **CONFIGURE** (KONFIGURÁLÁS) elemre.
9. Görgessen a **keys** (kulcsok) szakaszhoz, adja meg az időtartamot, majd kattintson a **SAVE** (MENTÉS) gombra. A lap ekkor frissül, és kijelzi a kulcs értékét. Az alkalmazásban ezt a kulcsértéket, valamint **CLIENT ID** (ÜGYFÉLAZONOSÍTÓ) értéket kell beállítani. (A konfigurálásra vonatkozó utasítások alkalmazásspecifikusak.)
10. Másolja ki a lapról az ügyfélazonosító értékét, mert ezzel fogja beállítani a tároló engedélyeit a következő lépésben.

## <a id="authorize"></a>A kulcs vagy titkos kód használatának engedélyezése az alkalmazás számára
Az alkalmazás a tároló kulcsához vagy titkos kódjához való hozzáférésének engedélyezéséhez használja a  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx) parancsmagot.

Ha például a tároló neve **ContosoKeyVault**, az engedélyezni kívánt alkalmazás ügyfélazonosítója 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, és engedélyezni szeretné az alkalmazás számára, hogy a tároló kulcsait visszafejtse és használja, futtassa az alábbi parancsot:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Ha engedélyezni szeretné, hogy az alkalmazás megnyithassa a tárolóban lévő titkos kódokat, futtassa az alábbi parancsot:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Hardveres biztonsági modul (HSM) használata
A nagyobb biztonság érdekében hardveres biztonsági modulokkal importálhat vagy hozhat létre a HSM határait mindig betartó kulcsokat. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Ha ez a követelmény nem vonatkozik Önre, ugorja át ezt a szakaszt, és folytassa a [Kulcsartó és a hozzá tartozó kulcsok és titkos kódok törlése](#delete) szakasszal.

HSM által védett kulcsok létrehozásához [HSM által védett kulcsokat támogató tárolóra való előfizetéssel](https://azure.microsoft.com/pricing/free-trial/) kell rendelkeznie.  Ezt a funkciót az Azure China nem támogatja.

A tároló létrehozásakor adja hozzá az alábbi **-SKU** paramétert:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Ehhez a tárolóhoz szoftveresen védett (korábban bemutatva) és HSM által védett kulcsokat is hozzáadhat. HSM által védett kulcs létrehozásához állítsa a **-Destination** paramétert „HSM”-re:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Az alábbi paranccsal egy .PFX-fájlban lévő kulcsot importálhat a számítógépéről. Ez a parancs a hardveres Key Vault szolgáltatás biztonsági moduljaiba importálja a kulcsot:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


A következő parancs egy „saját kulcs használata” (BYOK-) csomagot importál. Ezzel a forgatókönyvvel a helyi HSM-ben hozhatja létre a kulcsot, majd helyezheti át a Key Vault szolgáltatás HSM-jeibe anélkül, hogy a kulcs elhagyná a HSM határait:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

A BYOK-csomag létrehozásával kapcsolatos részletesebb útmutatásért tekintse meg a [How to generate and transfer HSM-protected keys for Azure Key Vault](key-vault-hsm-protected-keys.md) (HSM által védett kulcsok létrehozása és áthelyezése az Azure Key Vaultban) című témakört.

## <a id="delete"></a>Kulcstartó és a hozzá tartozó kulcsok és titkos kódok törlése
Ha már nincs szüksége a kulcstartóra és az ahhoz tartozó kulcsra vagy titkos kódra, eltávolíthatja a kulcstartót a [Remove-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx) parancsmaggal:

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Lehetősége van a teljes Azure-erőforráscsoport törlésére is, amely magában foglalja a kulcstartót és a csoport összes erőforrását:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Egyéb Azure PowerShell-parancsmagok
Egyéb parancsok, amelyek hasznosak lehetnek az Azure Key Vault kezeléséhez:

* `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Ez a parancs táblázatos formában jeleníti meg az összes kulcsot és a megadott tulajdonságokat.
* `$Keys[0]`: Ez a parancs a megadott kulcs tulajdonságainak teljes listáját jeleníti meg
* `Get-AzureKeyVaultSecret`: Ez a parancs táblázatos formában jeleníti meg az összes titkos kód nevét és a megadott tulajdonságokat.
* `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Példa egy adott kulcs eltávolítására.
* `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Példa egy adott titkos kód eltávolítására.

## <a id="next"></a>Következő lépések
Az Azure Key Vault webalkalmazásban való használatáról a [Use Azure Key Vault from a Web Application](key-vault-use-from-web-application.md) (Az Azure Key Vault webalkalmazással való használata) című témakörben találhat további útmutatást.

A kulcstartó használatának módjairól az [Azure Key Vault Logging](key-vault-logging.md) (Az Azure Key Vault naplózása) című témakörben olvashat.

A legújabb Azure Key Vaultra vonatkozó Azure PowerShell-parancsmagok listáját az [Azure Key Vault Cmdlets](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx) (Az Azure Key Vault parancsmagjai) című témakörben találja. 

Programozási hivatkozások: [Az Azure Key Vault fejlesztői útmutatója](key-vault-developers-guide.md).

<!--HONumber=Oct16_HO3-->


