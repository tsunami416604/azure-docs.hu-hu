---
title: Az Azure Storage-ba másolhatja vagy áthelyezheti az AzCopy v10 használatával | Microsoft Docs
description: A AzCopy egy parancssori segédprogram, amellyel az adatok átmásolhatók a, a-ból vagy a Storage-fiókok között. Ez a cikk segítséget nyújt a AzCopy letöltésében, a Storage-fiókhoz való kapcsolódásban, majd a fájlok átvitelében.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8701fe6857e95334a5e1d24bfe70feb130d5512c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756028"
---
# <a name="get-started-with-azcopy"></a>Bevezetés az AzCopy használatába

A AzCopy olyan parancssori segédprogram, amellyel blobokat vagy fájlokat másolhat a Storage-fiókba, vagy átmásolhatja azokat. Ez a cikk segítséget nyújt a AzCopy letöltésében, a Storage-fiókhoz való kapcsolódásban, majd a fájlok átvitelében.

> [!NOTE]
> A AzCopy **v10** a AzCopy jelenleg támogatott verziója.
>
> Ha a AzCopy egy korábbi verzióját kell használnia, tekintse meg a jelen cikk a [AzCopy előző verziójának használata](#previous-version) című szakaszát.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>AzCopy letöltése

Először töltse le a AzCopy V10-es végrehajtható fájlt a számítógép bármely könyvtárába. A AzCopy v10 csak egy végrehajtható fájl, ezért nem kell telepítenie.

- [Windows 64 bites](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32 bites](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Ezek a fájlok zip-fájlként (Windows és Mac) vagy tar-fájlként (Linux) vannak tömörítve. A kátrányos fájl Linux rendszeren való letöltéséhez és kibontásához tekintse meg a Linux-disztribúció dokumentációját.

> [!NOTE]
> Ha át szeretné másolni az adatait az [Azure Table Storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) szolgáltatásba és onnan, akkor telepítse az [AzCopy 7,3](https://aka.ms/downloadazcopynet)-es verzióját.


## <a name="run-azcopy"></a>AzCopy futtatása

A kényelmes használat érdekében érdemes lehet hozzáadni a AzCopy végrehajtható fájljának helyét a rendszer elérési útjához. Így bármilyen könyvtárból beírhatja `azcopy` a rendszerét.

Ha úgy dönt, hogy nem adja hozzá a AzCopy könyvtárat az elérési úthoz, akkor módosítania kell a címtárakat a AzCopy végrehajtható fájljának `azcopy` és `.\azcopy` típusának, vagy a Windows PowerShell-parancssorban.

A parancsok listájának megtekintéséhez írja be `azcopy -h` a parancsot, majd nyomja le az ENTER billentyűt.

Egy adott parancs megismeréséhez egyszerűen adja meg a parancs nevét (például: `azcopy list -h`).

![Beágyazott Súgó](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Az egyes parancsok és parancsok paramétereit részletesen ismertető dokumentációban találja: [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> Az Azure Storage-fiók tulajdonosaként nem rendel automatikusan hozzáférési engedélyeket az adateléréshez. Mielőtt bármit is AzCopy, el kell döntenie, hogyan adja meg az engedélyezési hitelesítő adatokat a Storage szolgáltatás számára. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Adja meg, hogyan adja meg az engedélyezési hitelesítő adatokat

Az engedélyezési hitelesítő adatokat Azure Active Directory (AD) vagy közös hozzáférésű aláírási (SAS-) token használatával biztosíthatja.

A táblázat használata útmutatóként:

| Tárolási típus | Jelenleg támogatott hitelesítési módszer |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**BLOB Storage (hierarchikus névtér)** | Azure AD & SAS |
|**File Storage** | Csak SAS |

### <a name="option-1-use-azure-active-directory"></a>1. lehetőség: a Azure Active Directory használata

Azure Active Directory használatával hitelesítő adatokat adhat meg, ahelyett, hogy SAS-tokent kellene hozzáfűzni az egyes parancsokhoz.  

> [!NOTE]
> Ha a jelenlegi kiadásban Blobok másolását tervezi a Storage-fiókok között, akkor minden forrás URL-címhez hozzá kell fűzni egy SAS-jogkivonatot. Az SAS-tokent csak a cél URL-címről hagyhatja ki. Példák: [Blobok másolása a Storage-fiókok között](storage-use-azcopy-blobs.md).

A szükséges engedély szintje attól függ, hogy fájlokat tölt fel, vagy csak letölti őket.

Ha csak le szeretné tölteni a fájlokat, ellenőrizze, hogy a [tároló blob-Adatolvasója](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) hozzá van-e rendelve a felhasználói identitáshoz, a felügyelt identitáshoz vagy az egyszerű szolgáltatáshoz.

> A felhasználói identitások, a felügyelt identitások és az egyszerű szolgáltatások mindegyike *rendszerbiztonsági tag*, ezért a jelen cikk további részében a *rendszerbiztonsági tag* kifejezést fogjuk használni.

Ha fájlokat szeretne feltölteni, ellenőrizze, hogy a következő szerepkörök egyike van-e hozzárendelve a rendszerbiztonsági tag számára:

- [Storage blob adatközreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Storage blob-adattulajdonos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Ezeket a szerepköröket a következő hatókörök bármelyikében lehet hozzárendelni a rendszerbiztonsági tag számára:

- Tároló (fájlrendszer)
- Tárfiók
- Erőforráscsoport
- Előfizetés

A szerepkörök ellenőrzésével és hozzárendelésével kapcsolatos további információkért lásd: [hozzáférés biztosítása az Azure blobhoz és a várólista adataihoz a Azure Portal RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Ne feledje, hogy a RBAC szerepkör-hozzárendelések akár öt percet is igénybe vehetnek.

Ha a rendszerbiztonsági tag hozzá van adva a célként megadott tároló vagy könyvtár hozzáférés-vezérlési listájához (ACL), nem kell rendelkeznie a rendszerbiztonsági tag számára hozzárendelt szerepkörök valamelyikével. Az ACL-ben a rendszerbiztonsági tag írási engedélyre van szüksége a célhely könyvtárához, és a tárolón és minden szülő könyvtáron végre kell hajtania az engedélyeket.

További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Felhasználói identitás hitelesítése

Miután meggyőződött arról, hogy a felhasználói identitás megkapta a szükséges engedélyezési szintet, nyisson meg egy parancssort, írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login
```

Ha egynél több szervezethez tartozik, adja meg annak a szervezetnek a bérlői AZONOSÍTÓját, amelyhez a Storage-fiók tartozik.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Cserélje le `<tenant-id>` a helyőrzőt annak a szervezetnek a BÉRLŐi azonosítójával, amelyhez a Storage-fiók tartozik. A bérlő AZONOSÍTÓjának megkereséséhez válassza a Azure Portal **Azure Active Directory > tulajdonságok > a könyvtár azonosítóját** .

Ez a parancs egy hitelesítő kódot és egy webhely URL-címét adja vissza. Nyissa meg a webhelyet, adja meg a kódot, majd válassza a **tovább** gombot.

![Tároló létrehozása](media/storage-use-azcopy-v10/azcopy-login.png)

Ekkor megjelenik egy bejelentkezési ablak. Ebben az ablakban jelentkezzen be az Azure-fiókjába az Azure-fiókja hitelesítő adataival. Miután sikeresen bejelentkezett, lezárhatja a böngészőablakot, és megkezdheti a AzCopy használatát.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Egyszerű szolgáltatásnév hitelesítése

Ez nagyszerű megoldás, ha a AzCopy-t olyan parancsfájlon belül szeretné használni, amely felhasználói beavatkozás nélkül fut, különösen ha a helyszínen fut. Ha az Azure-ban futó virtuális gépeken szeretné futtatni a AzCopy-t, a felügyelt szolgáltatás identitása könnyebben felügyelhető. További információt a cikk [felügyelt identitások hitelesítése](#managed-identity) című szakaszában talál.

A szkript futtatása előtt interaktívan kell bejelentkeznie legalább egyszer, hogy AzCopy biztosítson a szolgáltatásnév hitelesítő adataival.  Ezeket a hitelesítő adatokat egy biztonságos és titkosított fájlban tárolja a rendszer, így a parancsfájlnak nem kell megadnia a bizalmas adatokat.

Bejelentkezhet a fiókjába egy ügyfél titkos kódjával vagy egy olyan tanúsítvány jelszavával, amely társítva van a szolgáltatásnév alkalmazásának regisztrálásához.

Ha többet szeretne megtudni az egyszerű szolgáltatásnév létrehozásáról, tekintse meg a [How to: use The Portal for Azure ad-alkalmazás és egyszerű szolgáltatásnév, amely hozzáfér az erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Az egyszerű szolgáltatásokkal kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Ügyfél-titkos kód használata

Először állítsa be a `AZCOPY_SPA_CLIENT_SECRET` környezeti változót az egyszerű szolgáltatásnév alkalmazás-regisztrációjának ügyfél-titkára.

> [!NOTE]
> Ügyeljen rá, hogy ezt az értéket adja meg a parancssorból, és ne az operációs rendszer környezeti változó beállításait. Így az érték csak az aktuális munkamenet számára érhető el.

Ez a példa bemutatja, hogyan teheti ezt meg a PowerShellben.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Érdemes lehet egy parancssort használni, ahogy az ebben a példában is látható. Így a jelszó nem jelenik meg a konzolon a parancs előzményeiben.  

Ezután írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Cserélje le `<application-id>` a helyőrzőt a szolgáltatásbeli tag alkalmazás-regisztrációjának alkalmazás-azonosítójával. Cserélje le `<tenant-id>` a helyőrzőt annak a szervezetnek a BÉRLŐi azonosítójával, amelyhez a Storage-fiók tartozik. A bérlő AZONOSÍTÓjának megkereséséhez válassza a Azure Portal **Azure Active Directory > tulajdonságok > a könyvtár azonosítóját** . 

##### <a name="using-a-certificate"></a>Tanúsítvány használata

Ha inkább a saját hitelesítő adatait szeretné használni az engedélyezéshez, feltöltheti a tanúsítványt az alkalmazás regisztrálásához, majd ezt a tanúsítványt használhatja a bejelentkezéshez.

A tanúsítványnak az alkalmazás regisztrálásához való feltöltése mellett a tanúsítvány másolatát is el kell végezni a gépre vagy a virtuális gépre, ahol a AzCopy futni fog. A tanúsítvány ezen másolatának a következőnek kell lennie:. PFX vagy. PEM formátuma, és tartalmaznia kell a titkos kulcsot. A titkos kulcsnak jelszóval védettnek kell lennie. Ha Windows rendszert használ, és a tanúsítvány csak tanúsítványtárolóban létezik, ügyeljen arra, hogy a tanúsítványt egy PFX-fájlba exportálja (beleértve a titkos kulcsot is). Útmutatásért lásd: [export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Ezután állítsa a `AZCOPY_SPA_CERT_PASSWORD` környezeti változót a tanúsítvány jelszavára.

> [!NOTE]
> Ügyeljen rá, hogy ezt az értéket adja meg a parancssorból, és ne az operációs rendszer környezeti változó beállításait. Így az érték csak az aktuális munkamenet számára érhető el.

Ebből a példából megtudhatja, hogyan teheti ezt a feladatot a PowerShellben.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Ezután írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Cserélje le `<path-to-certificate-file>` a helyőrzőt a tanúsítványfájl relatív vagy teljesen minősített elérési útjára. A AzCopy menti a tanúsítvány elérési útját, de nem menti a tanúsítvány másolatát, ezért ügyeljen arra, hogy a tanúsítvány a helyén maradjon. Cserélje le `<tenant-id>` a helyőrzőt annak a szervezetnek a BÉRLŐi azonosítójával, amelyhez a Storage-fiók tartozik. A bérlő AZONOSÍTÓjának megkereséséhez válassza a Azure Portal **Azure Active Directory > tulajdonságok > a könyvtár azonosítóját** .

> [!NOTE]
> Érdemes lehet egy parancssort használni, ahogy az ebben a példában is látható. Így a jelszó nem jelenik meg a konzolon a parancs előzményeiben. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Felügyelt identitás hitelesítése

Ez nagyszerű megoldás, ha a AzCopy-t olyan parancsfájlon belül szeretné használni, amely felhasználói beavatkozás nélkül fut, és a szkript egy Azure-beli virtuális gépről (VM) fut. Ha ezt a beállítást használja, nem kell semmilyen hitelesítő adatot tárolnia a virtuális gépen.

A fiókba a rendszerszintű felügyelt identitás használatával jelentkezhet be, amelyet engedélyezett a virtuális gépen, vagy a virtuális géphez hozzárendelt, felhasználó által hozzárendelt felügyelt identitás ügyfél-AZONOSÍTÓjának, Objektumazonosítóának vagy erőforrás-AZONOSÍTÓjának használatával.

Ha többet szeretne megtudni a rendszerszintű felügyelt identitás engedélyezéséről vagy a felhasználó által hozzárendelt felügyelt identitás létrehozásáról, tekintse [meg a felügyelt identitások konfigurálása az Azure-erőforrásokhoz a Azure Portal használatával](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)című témakört.

##### <a name="using-a-system-wide-managed-identity"></a>Rendszerszintű felügyelt identitás használata

Először is győződjön meg arról, hogy engedélyezte a rendszerszintű felügyelt identitást a virtuális gépen. Tekintse meg a [rendszer által hozzárendelt felügyelt identitást](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Ezután a parancssorba írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás használata

Először is győződjön meg arról, hogy engedélyezte a felhasználó által hozzárendelt felügyelt identitást a virtuális gépen. Tekintse meg a [felhasználó által hozzárendelt felügyelt identitást](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Ezután a parancssorba írja be a következő parancsok bármelyikét, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Cserélje le `<client-id>` a helyőrzőt a felhasználó által hozzárendelt felügyelt identitás ügyfél-azonosítójával.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Cserélje le `<object-id>` a helyőrzőt a felhasználó által hozzárendelt felügyelt identitás objektum-azonosítójával.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Cserélje le `<resource-id>` a helyőrzőt a felhasználó által hozzárendelt felügyelt identitás erőforrás-azonosítójával.

### <a name="option-2-use-a-sas-token"></a>2. lehetőség: SAS-token használata

A AzCopy-parancsokban használt minden forráshoz vagy cél URL-címhez hozzáfűzhető SAS-token.

A példában szereplő parancs rekurzív módon másolja át az adatait egy helyi könyvtárból egy blob-tárolóba. A rendszer kitalált SAS-jogkivonatot fűz a tároló URL-címének végéhez.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Ha többet szeretne megtudni az SAS-jogkivonatokról és a beszerzéséről, tekintse meg a [közös hozzáférésű aláírások (SAS) használata](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)című témakört.

## <a name="transfer-files"></a>Fájlok átvitele

Miután hitelesítette identitását, vagy SAS-tokent kapott, megkezdheti a fájlok átvitelét.

A parancsok megkereséséhez tekintse meg a cikkek bármelyikét.

- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)

- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)

- [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

- [Adatok átvitele a AzCopy és a Azure Stack Storage szolgáltatással](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>AzCopy használata parancsfájlban

### <a name="obtain-a-static-download-link"></a>Statikus letöltési hivatkozás beszerzése

Idővel a AzCopy [letöltési hivatkozása](#download-and-install-azcopy) a AzCopy új verzióira mutat. Ha a parancsfájl letölti a AzCopy-t, a parancsfájl leállhat, ha a AzCopy újabb verziója módosítja a parancsfájltól függő funkciókat.

A probléma elkerüléséhez szerezzen be egy statikus (nem módosítható) hivatkozást a AzCopy aktuális verziójára. Így a parancsfájl a AzCopy azonos pontos verzióját tölti le minden egyes futtatásakor.

A hivatkozás beszerzéséhez futtassa a következő parancsot:

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Linux esetén `--strip-components=1` a `tar` parancs eltávolítja a verziószámot tartalmazó legfelső szintű mappát, a bináris fájlt pedig közvetlenül az aktuális mappába bontja ki. Ez lehetővé teszi, hogy a parancsfájl a új verziójával `azcopy` frissüljön, és csak az `wget` URL-címet frissítse.

Az URL-cím megjelenik a parancs kimenetében. A szkript ezután letöltheti a AzCopy az adott URL-cím használatával.

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Speciális karakterek elmenekülése SAS-jogkivonatokban

A `.cmd` kiterjesztésű batch-fájlokban el kell menekülnie a `%` sas-jogkivonatokban megjelenő karaktereket. Ezt úgy teheti meg, hogy hozzáad `%` egy további KARAKTERT az `%` sas-jogkivonat karakterláncának meglévő karaktereinek mellett.

### <a name="run-scripts-by-using-jenkins"></a>Parancsfájlok futtatása a Jenkins használatával

Ha azt tervezi, hogy a [Jenkins](https://jenkins.io/) használatával futtatja a parancsfájlokat, ügyeljen arra, hogy a parancsfájl elején helyezze el a következő parancsot.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>AzCopy használata Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a AzCopy használatával hajtja végre az összes adatátviteli műveletet. Ha a AzCopy teljesítménybeli előnyeit szeretné kihasználni, használhatja a [Storage Explorert](https://azure.microsoft.com/features/storage-explorer/) , de a parancssor helyett inkább grafikus felhasználói felületet szeretne használni a fájlokkal való kommunikációhoz.

Storage Explorer a fiók kulcsát használja a műveletek végrehajtásához, ezért a Storage Explorer való bejelentkezés után nem kell további hitelesítési hitelesítő adatokat megadnia.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>A AzCopy korábbi verziójának használata

Ha a AzCopy előző verzióját kell használnia, tekintse meg az alábbi hivatkozások egyikét:

- [AzCopy Windowson (8-as verzió)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy Linux rendszeren (v7)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy konfigurálása, optimalizálása és megoldása

Lásd: [AzCopy konfigurálása, optimalizálása és hibáinak megoldása](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>További lépések

Ha kérdése van, problémája vagy általános visszajelzése van, küldje el őket [a GitHub](https://github.com/Azure/azure-storage-azcopy) oldalon.
