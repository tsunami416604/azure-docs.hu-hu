---
title: Adatok másolása vagy áthelyezése az Azure Storage-ba az AzCopy v10 használatával | Microsoft dokumentumok
description: Az AzCopy egy parancssori segédprogram, amely segítségével adatokat másolhat, onnan vagy a tárfiókok között. Ez a cikk segít az AzCopy letöltésében, a tárfiókhoz való csatlakozásban, majd a fájlok átvitelében.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 90adb3b973b6952d10ef89e4b40d0fc68489e224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061110"
---
# <a name="get-started-with-azcopy"></a>Bevezetés az AzCopy használatába

Az AzCopy egy parancssori segédprogram, amely segítségével blobok vagy fájlok másolása egy tárfiókba. Ez a cikk segít az AzCopy letöltésében, a tárfiókhoz való csatlakozásban, majd a fájlok átvitelében.

> [!NOTE]
> AzAzCopy **V10** az AzCopy jelenleg támogatott verziója.
>
> Ha az AzCopy **v8.1-es verzióját**szeretné használni, olvassa el a cikk [AzCopy korábbi verziójának használata](#previous-version) című szakaszát.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Az AzCopy letöltése

Először töltse le az AzCopy V10 végrehajtható fájlt a számítógép bármely könyvtárába. AzCopy V10 csak egy futtatható fájl, így nincs mit telepíteni.

- [64 bites Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32 bites](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (kátrány)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Ezek a fájlok tömörített, mint egy zip fájl (Windows és Mac) vagy egy kátrányos fájl (Linux). A kátrányos fájl Linuxon történő letöltéséhez és kicsomagolásához tekintse meg a Linux-disztribúció dokumentációját.

> [!NOTE]
> Ha adatokat szeretne másolni az [Azure Table storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) szolgáltatásba és onnan, telepítse az [AzCopy 7.3-as verzióját.](https://aka.ms/downloadazcopynet)


## <a name="run-azcopy"></a>AzCopy futtatása

A kényelem érdekében fontolja meg az AzCopy végrehajtható fájl könyvtárának helyét a rendszer elérési útjára a könnyű használat érdekében. Így a rendszer `azcopy` bármely könyvtárából gépelhet.

Ha úgy dönt, hogy nem adja hozzá az AzCopy könyvtárat az elérési úthoz, a `azcopy` könyvtárakat az AzCopy végrehajtható fájl helyére kell módosítania, és írja be `.\azcopy` a Windows PowerShell parancssorait.

A parancsok listájának megtekintéséhez írja be, `azcopy -h` majd nyomja le az ENTER billentyűt.

Ha egy adott parancsról szeretne többet megtudni, csak `azcopy list -h`adja meg a parancs nevét (például: ).

![Szövegközi súgó](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Az egyes parancsok és parancsparaméterek részletes referenciadokumentációjának megtekintéséhez lásd: [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> Az Azure Storage-fiók tulajdonosaként nem kap automatikusan engedélyeket az adatok eléréséhez. Mielőtt bármit értelmes az AzCopy, el kell döntenie, hogyan adja meg az engedélyezési hitelesítő adatokat a tárolási szolgáltatás. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Adja meg, hogyan adja meg az engedélyezési hitelesítő adatokat

Az Azure Active Directory (AD) vagy a megosztott hozzáférésű aláírás (SAS) jogkivonat használatával engedélyezési hitelesítő adatokat adhat meg.

Használja ezt a táblázatot útmutatóként:

| Tárolási típus | Jelenleg támogatott engedélyezési módszer |
|--|--|
|**Blob-tároló** | Azure AD & SAS |
|**Blob storage (hierarchikus névtér)** | Azure AD & SAS |
|**Fájltárolás** | Csak SAS |

### <a name="option-1-use-azure-active-directory"></a>1. lehetőség: Az Azure Active Directory használata

Az Azure Active Directory használatával egyszer adhat meg hitelesítő adatokat ahelyett, hogy minden parancshoz hozzá kellene fűznie egy SAS-jogkivonatot.  

> [!NOTE]
> Az aktuális kiadásban, ha azt tervezi, hogy blobok között tárfiókok közötti másolása, minden egyes forrás URL-cím hez hozzá kell fűznie egy SAS-jogkivonatot. A SAS-jogkivonatot csak a cél URL-címből hagyhatja ki. Példák: [Blobok másolása a tárfiókok között.](storage-use-azcopy-blobs.md)

A szükséges engedélyezési szint attól függ, hogy fájlokat kíván feltölteni, vagy csak letölteni őket.

Ha csak fájlokat szeretne letölteni, ellenőrizze, hogy a [Storage Blob Data Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) hozzá van-e rendelve a felhasználói identitáshoz, a felügyelt identitáshoz vagy az egyszerű szolgáltatáshoz.

> A felhasználói identitások, a felügyelt identitások és a szolgáltatásnévi tagok egy-egy *rendszerbiztonsági tag*típusúak, ezért a cikk többi részében a *rendszerbiztonsági* tag kifejezést használjuk.

Ha fájlokat szeretne feltölteni, ellenőrizze, hogy a szerepkörök valamelyike hozzá van-e rendelve a rendszerbiztonsági taghoz:

- [Storage Blob-adatközreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Tárolási blob-adatok tulajdonosa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Ezek a szerepkörök a rendszerbiztonsági taghoz rendelhetők a következő hatókörök bármelyikében:

- Tároló (fájlrendszer)
- Tárfiók
- Erőforráscsoport
- Előfizetés

A szerepkörök ellenőrzéséről és hozzárendeléséről az [Azure-blob- és várólista-adatok hoz való hozzáférés megadása az RBAC-mal című témakörben olvashat az Azure Portalon.](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

> [!NOTE]
> Ne feledje, hogy az RBAC szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet.

Nem kell, hogy a rendszerbiztonsági taghoz ilyen szerepkörök tartoznak, ha a rendszerbiztonsági tag hozzá van adva a céltároló vagy könyvtár hozzáférés-vezérlési listájához.You don't need to have one of these roles signed to your security tag if your security tag is added to the access control list (ACL) of the target container or directory. Az ACL-ben a rendszerbiztonsági tagnak írási engedélyt kell adnia a célkönyvtárhoz, és engedélyt kell végrehajtania a tárolóra és az egyes szülőkönyvtárakra.

További információ: [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Felhasználói identitás hitelesítése

Miután meggyőződött arról, hogy a felhasználói identitás megkapta a szükséges engedélyezési szintet, nyisson meg egy parancssort, írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login
```

Ha egynél több szervezethez tartozik, adja meg annak a szervezetnek a bérlői azonosítóját, amelyhez a tárfiók tartozik.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Cserélje `<tenant-id>` le a helyőrzőt annak a szervezetnek a bérlői azonosítójára, amelyhez a tárfiók tartozik. A bérlői azonosító megkereséséhez válassza az **Azure Active Directory > properties > Directory-azonosítót** az Azure Portalon.

Ez a parancs egy hitelesítési kódot és egy webhely URL-címét adja vissza. Nyissa meg a webhelyet, adja meg a kódot, majd kattintson a **Tovább** gombra.

![Tároló létrehozása](media/storage-use-azcopy-v10/azcopy-login.png)

Megjelenik egy bejelentkezési ablak. Ebben az ablakban jelentkezzen be az Azure-fiókjába az Azure-fiók hitelesítő adataival. Miután sikeresen bejelentkezett, bezárhatja a böngészőablakot, és elkezdheti használni az AzCopy programot.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Egyszerű szolgáltatás hitelesítése

Ez egy nagyszerű lehetőség, ha azt tervezi, hogy az AzCopy egy parancsfájlon belül, amely fut felhasználói beavatkozás nélkül, különösen a helyszíni futtatásakor. Ha azt tervezi, hogy az AzCopy-t az Azure-ban futó virtuális gépeken futtatja, a felügyelt szolgáltatásidentitás könnyebben felügyelheti. További információ: A cikk [Felügyelt identitás hitelesítése](#managed-identity) című szakaszában olvashat.

A parancsfájl futtatása előtt legalább egyszer be kell jelentkeznie interaktívan, hogy az AzCopy-t a szolgáltatásegyszerű hitelesítő adataival eltudja adni.  Ezeket a hitelesítő adatokat egy biztonságos és titkosított fájl tárolja, így a parancsfájlnak nem kell megadnia a bizalmas adatokat.

Bejelentkezhet a fiókjába egy ügyféltitok használatával vagy a szolgáltatásegyszerű szolgáltatás alkalmazásregisztrációjához társított tanúsítvány jelszavával.

Ha többet szeretne tudni az egyszerű szolgáltatás létrehozásáról: [Útmutató: A portál használata az erőforrásokhoz hozzáférő Azure AD-alkalmazás és egyszerű szolgáltatás létrehozásához](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)című témakörben olvashat bővebben.

Ha többet szeretne megtudni a szolgáltatásegyszerű szolgáltatásokról, olvassa el [az Alkalmazás- és egyszerű szolgáltatásobjektumok az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Ügyféltitok használata

Első ként `AZCOPY_SPA_CLIENT_SECRET` a környezeti változó titka a szolgáltatás névjegyzékbe vételi szolgáltatás titka.

> [!NOTE]
> Győződjön meg arról, hogy ezt az értéket a parancssorból állítja be, és nem az operációs rendszer környezeti változóbeállításaiban. Így az érték csak az aktuális munkamenet számára érhető el.

Ez a példa bemutatja, hogyan teheti ezt meg a PowerShellben.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Érdemes lehet a példában látható kérdés használatát használni. Így a jelszó nem jelenik meg a konzol parancselőzményeiközött.  

Ezután írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Cserélje `<application-id>` le a helyőrzőt a szolgáltatásnév alkalmazásregisztrációjának alkalmazásazonosítójára. Cserélje `<tenant-id>` le a helyőrzőt annak a szervezetnek a bérlői azonosítójára, amelyhez a tárfiók tartozik. A bérlői azonosító megkereséséhez válassza az **Azure Active Directory > properties > Directory-azonosítót** az Azure Portalon. 

##### <a name="using-a-certificate"></a>Tanúsítvány használata

Ha a saját hitelesítő adatait szeretné használni az engedélyezéshez, feltölthet egy tanúsítványt az alkalmazás regisztrációjába, majd ezt a tanúsítványt használhatja a bejelentkezéshez.

A tanúsítvány alkalmazásregisztrációba való feltöltése mellett a tanúsítvány egy példányát is hozzá kell mentenie a számítógépre vagy a virtuális gépre, ahol az AzCopy futni fog. A tanúsítvány ezen példányának a programban kell lennie. PFX vagy . PEM formátumban, és tartalmaznia kell a személyes kulcsot. A személyes kulcsot jelszóval kell védeni. Ha Windows rendszert használ, és a tanúsítvány csak egy tanúsítványtárolóban található, győződjön meg arról, hogy a tanúsítványt PFX-fájlba exportálja (beleértve a személyes kulcsot is). Útmutatásért [lásd: Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Ezután állítsa `AZCOPY_SPA_CERT_PASSWORD` a környezeti változót a tanúsítvány jelszavára.

> [!NOTE]
> Győződjön meg arról, hogy ezt az értéket a parancssorból állítja be, és nem az operációs rendszer környezeti változóbeállításaiban. Így az érték csak az aktuális munkamenet számára érhető el.

Ez a példa bemutatja, hogyan végezheti el ezt a feladatot a PowerShellben.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Ezután írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Cserélje `<path-to-certificate-file>` le a helyőrzőt a tanúsítványfájl relatív vagy teljesen minősített elérési parancsára. AzAzCopy menti a tanúsítvány elérési útját, de nem menti a tanúsítvány egy példányát, ezért ügyeljen arra, hogy a tanúsítvány a helyén maradjon. Cserélje `<tenant-id>` le a helyőrzőt annak a szervezetnek a bérlői azonosítójára, amelyhez a tárfiók tartozik. A bérlői azonosító megkereséséhez válassza az **Azure Active Directory > properties > Directory-azonosítót** az Azure Portalon.

> [!NOTE]
> Érdemes lehet a példában látható kérdés használatát használni. Így a jelszó nem jelenik meg a konzol parancselőzményeiközött. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Felügyelt identitás hitelesítése

Ez egy nagyszerű lehetőség, ha azt tervezi, hogy az AzCopy egy parancsfájlon belül, amely fut felhasználói beavatkozás nélkül, és a parancsfájl fut egy Azure virtuális gép (VM). Ha ezt a lehetőséget használja, nem kell tárolnia a hitelesítő adatokat a virtuális gépen.

A virtuális gépen engedélyezett rendszerszintű felügyelt identitás használatával, vagy a virtuális géphez rendelt, felhasználó által hozzárendelt felügyelt identitás ügyfélazonosítójával, objektumazonosítójával vagy erőforrás-azonosítójával jelentkezhet be a fiókjába.

Ha többet szeretne tudni arról, hogy miként engedélyezheti a rendszerszintű felügyelt identitást, vagy hogyan hozhat létre egy felhasználó által hozzárendelt felügyelt identitást, olvassa [el az Azure-erőforrások felügyelt identitásának konfigurálása virtuális gépen az Azure Portalon című témakört.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)

##### <a name="using-a-system-wide-managed-identity"></a>Rendszerszintű felügyelt identitás használata

Először győződjön meg arról, hogy engedélyezte a rendszer szintű felügyelt identitás a virtuális gép. Lásd: [Rendszeráltal hozzárendelt felügyelt identitás.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)

Ezután írja be a parancskonzolon a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás használata

Először győződjön meg arról, hogy engedélyezte a felhasználó által hozzárendelt felügyelt identitása a virtuális gép. Lásd: [Felhasználó által hozzárendelt felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Ezután írja be a parancskonzolon az alábbi parancsok bármelyikét, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Cserélje `<client-id>` le a helyőrzőt a felhasználó által hozzárendelt felügyelt identitás ügyfélazonosítójára.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Cserélje `<object-id>` le a helyőrzőt a felhasználó által hozzárendelt felügyelt identitás objektumazonosítójára.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Cserélje `<resource-id>` le a helyőrzőt a felhasználó által hozzárendelt felügyelt identitás erőforrásazonosítójára.

### <a name="option-2-use-a-sas-token"></a>2. lehetőség: SAS-jogkivonat használata

SAS-jogkivonatot fűzhet minden olyan forrás- vagy célURL-hez, amely az AzCopy-parancsokban használatos.

Ez a példa parancs rekurzívan másolja az adatokat egy helyi könyvtárból egy blob tárolóba. Egy fiktív SAS-jogkivonat hozzáfűzve a tároló URL-címének végén.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Ha többet szeretne megtudni a SAS-jogkivonatokról és azok beszerzéséről, olvassa el [a Közös hozzáférésű aláírások (SAS) használata című témakört.](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

## <a name="transfer-files"></a>Fájlok átvitele

Miután hitelesítette az identitását, vagy beszerzett egy SAS-jogkivonatot, megkezdheti a fájlok átvitelét.

A példaparancsok megkereséséhez tekintse meg ezeket a cikkeket.

- [Adatok átvitele az AzCopy és blob tárházával](storage-use-azcopy-blobs.md)

- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)

- [Adatátvitel az AzCopy és az Amazon S3 gyűjtőkkel](storage-use-azcopy-s3.md)

- [Adatok átvitele az AzCopy és az Azure Stack tárterülettel](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Az AzCopy használata parancsfájlban

### <a name="obtain-a-static-download-link"></a>Statikus letöltési hivatkozás beszerzése

Idővel az AzCopy [letöltési hivatkozás](#download-and-install-azcopy) az AzCopy új verzióira mutat. Ha a parancsfájl letölti az AzCopy programot, előfordulhat, hogy a parancsfájl nem fog működni, ha az AzCopy egy újabb verziója módosítja azokat a funkciókat, amelyektől a parancsfájl függ.

A problémák elkerülése érdekében szerezzen be egy statikus (változatlan) hivatkozást az AzCopy aktuális verziójára. Így a parancsfájl az AzCopy pontos verzióját tölti le minden egyes futtatáskor.

A hivatkozás beszerzéséhez futtassa a következő parancsot:

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Linux esetén `--strip-components=1` a `tar` parancs eltávolítja a verziónevet tartalmazó legfelső szintű mappát, és ehelyett közvetlenül az aktuális mappába bontja ki a bináris fájlt. Ez lehetővé teszi, hogy a `azcopy` parancsfájl továbbfejlesztett `wget` változata csak az URL-t frissíti.

Az URL-cím megjelenik a parancs kimenetében. A parancsfájl ezután letöltheti az AzCopy-t ezzel az URL-címmel.

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Különleges karakterek kiszabadulása a SAS-tokenekben

A kiterjesztést `.cmd` tartalmazó kötegfájlokban el kell `%` menekülnie a SAS-jogkivonatokban megjelenő karakterek elől. Ezt úgy teheti meg, `%` hogy a `%` SAS-jogkivonat-karakterlánc meglévő karakterei mellett további karaktert ad hozzá.

### <a name="run-scripts-by-using-jenkins"></a>Parancsfájlok futtatása a Jenkins használatával

Ha a [Jenkins](https://jenkins.io/) használatával parancsfájlokat kíván futtatni, győződjön meg arról, hogy a következő parancsot helyezi a parancsfájl elejére.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Az AzCopy használata az Azure Storage Explorerben

[A Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) az AzCopy segítségével hajtja végre az összes adatátviteli műveletet. A Storage Explorer t akkor [használhatja,](https://azure.microsoft.com/features/storage-explorer/) ha ki szeretné használni az AzCopy teljesítményelőnyeit, de a parancssor helyett grafikus felhasználói felületet szeretne használni a fájlok kezeléséhez.

A Storage Explorer a fiókkulcsot használja a műveletek végrehajtásához, így miután bejelentkezett a Storage Explorerprogramba, nem kell további engedélyezési hitelesítő adatokat megadnia.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Az AzCopy korábbi verziójának használata

Ha az AzCopy korábbi verzióját (AzCopy v8.1) szeretné használni, tekintse meg az alábbi hivatkozások egyikét:

- [AzCopy Windowson (8-as verzió)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy Linuxon (8-as verzió)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Az AzCopy konfigurálása, optimalizálása és hibaelhárítása

Lásd: [AzAzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>További lépések

Ha kérdése, problémája vagy általános visszajelzése van, küldje el őket [a GitHub-lapon.](https://github.com/Azure/azure-storage-azcopy)
