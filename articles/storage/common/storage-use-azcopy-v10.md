---
title: Másolja, vagy az Azure Storage-adatok áthelyezése az AzCopy v10 |} A Microsoft Docs
description: Az AzCopy parancssori segédprogram, amely segítségével történő, a vagy tárfiókok között az adatok másolása. Ez a cikk segít az AzCopy letöltése, a tárfiókhoz való csatlakozáshoz, és majd a fájlok átvitelét.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 722097f1a61a10cd45c0c330e998021cd1abf0c8
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147964"
---
# <a name="get-started-with-azcopy"></a>Bevezetés az AZCopy használatába

Az AzCopy parancssori segédprogram, amely a blobok és a fájlok másolása, illetve a storage-fiók használatával. Ez a cikk segít az AzCopy letöltése, a tárfiókhoz való csatlakozáshoz, és majd a fájlok átvitelét.

> [!NOTE]
> Az AzCopy **V10** az AzCopy a jelenleg támogatott verzió.
>
> Ha az AzCopy használata kell **v8.1**, tekintse meg a [korábbi verzióját használja, az AzCopy](#previous-version) című szakaszát.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Töltse le az AzCopy

Először töltse le az AzCopy V10 végrehajtható fájl bármilyen könyvtárat a számítógépen. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Ha szeretné, amelyekből az adatok másolása a [Azure Table storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) szolgáltatásra, majd telepítse [AzCopy 7.3-as verzió](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Az AzCopy futtatása

Kényelmi célokat szolgál fontolja meg a könyvtár helyét a AzCopy végrehajtható fájl hozzáadása a használat megkönnyítése érdekében a rendszer elérési útjához. Ezzel a módszerrel írhatja `azcopy` bármelyik könyvtárból a rendszeren.

Ha nem kíván az AzCopy könyvtár hozzáadása az elérési úthoz, kell módosítsa a könyvtárakat az AzCopy végrehajtható fájlt, és írja be a helyére `azcopy` vagy `.\azcopy` Windows PowerShell-parancs felszólítja a.

Parancsok listájának megtekintéséhez írja be a `azcopy -h` és nyomja le az ENTER billentyűt.

Egy bizonyos paranccsal kapcsolatos további információkért csak a parancs neve is (például: `azcopy list -h`).

![Beágyazott Súgó](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> Az Azure Storage-fiók tulajdonosai automatikusan nem hozzárendelt engedélyeket az adatok eléréséhez. Akkor is végrehajthat az Azcopyval jelentéssel bíró, mielőtt szüksége annak eldöntése, hogyan ehhez meg kell adnia engedélyezési hitelesítő adatok a storage szolgáltatásra. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Válassza ki, hogyan fogja engedélyezési hitelesítő adatok megadása

Megadhatja az engedélyezési hitelesítő adatok az Azure Active Directory (AD) használatával, illetve egy közös hozzáférésű Jogosultságkód (SAS) token használatával.

Ez a tábla használja útmutatóként:

| Tárolási típus | Az engedély jelenleg támogatott metódus |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**A BLOB storage (idősorozatokra névtér)** | Csak Azure ad-ben |
|**A File storage** | Csak a SAS |

### <a name="option-1-use-azure-ad"></a>Option 1: Az Azure AD használata

Engedély szükséges a szint alapul e azt tervezi, hogy a fájlok feltöltése vagy egyszerűen letöltheti a fájlokat.

Ha csak szeretné letölteni a fájlokat, majd ellenőrizze, hogy a [Storage-Blobadatok olvasója](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) hozzá lett rendelve a személyazonosságát.

Ha azt szeretné, a fájlok feltöltése, majd győződjön meg arról, hogy egyik szerepkör van rendelve az identitás:

- [Storage-Blobadatok Közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Tárolási Blob adatok tulajdonosa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Ezek a szerepkörök rendelhetők az identitás, ezek a hatókörök egyikében:

- Tároló (fájlrendszer)
- Tárfiók
- Erőforráscsoport
- Előfizetés

Győződjön meg arról, és szerepkörök hozzárendelése kapcsolatban lásd: [hozzáférést biztosít az Azure blob és üzenetsor az adatokat az RBAC az Azure Portalon](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Nem kell egy ilyen szerepkörbe, rendelt személyazonosságát, ha az identitás bekerül a céltároló vagy könyvtár hozzáférés-vezérlési lista (ACL) rendelkezik. A hozzáférés-vezérlési listában az identitás írási engedélye a célként megadott könyvtárban, és végrehajtási engedéllyel a tárolót és minden egyes szülőkönyvtárhoz van szüksége.

További tudnivalókért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>A személyazonosság hitelesítéséhez

Miután meggyőződött arról, hogy az identitás megadta-e a szükséges jogosultsági szintet, nyisson meg egy parancssort, írja be a következő parancsot, és nyomja le az ENTER billentyűt.

```azcopy
azcopy login
```

Ha több mint egy szervezethez tartozik, például a bérlő azonosítója, amelyhez a storage-fiók tartozik a szervezet.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Cserélje le a `<tenant-id>` helyőrzőt a bérlő Azonosítóját, a szervezet, amely a tárfiók tartozik. A bérlő Azonosítójának megkereséséhez válassza ki a **Azure Active Directory > Tulajdonságok > címtár-azonosító** az Azure Portalon.

Ez a parancs visszaadja a hitelesítési kódot és a egy webhely URL-CÍMÉT. Nyissa meg a webhelyet, írja be a kódot, és válassza a **tovább** gombra.

![Tároló létrehozása](media/storage-use-azcopy-v10/azcopy-login.png)

A bejelentkezési ablak jelenik meg. Ezt az ablakot jelentkezzen be Azure-fiókjába az Azure-fiók hitelesítő adataival. Miután sikeresen bejelentkezett, zárja be a böngészőablakot, és megkezdheti az AzCopy használatával.

### <a name="option-2-use-a-sas-token"></a>Option 2: SAS-tokennel

Az AzCopy-parancsokat az SAS-token minden forrás vagy cél URL-címet használó fűzze hozzá.

A példában a parancs rekurzív módon adatokat másol egy blob-tároló egy helyi könyvtárból. Egy fiktív SAS-token végén a rendszer hozzáfűzi a tároló URL-címet.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS-tokeneket és a egy beszerzésével kapcsolatos további tudnivalókért lásd: [a közös hozzáférésű jogosultságkód (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Fájlok átvitele

Miután hitelesített személyazonosságát, vagy beszerzett SAS-jogkivonatát, elkezdheti a fájlok átvitelével az.

Példaparancsok megkereséséhez tekintse meg az alábbi cikkek valamelyikére.

- [Adatok áthelyezése az AzCopy és a blob storage](storage-use-azcopy-blobs.md)

- [Adatok áthelyezése az AzCopy és a file storage](storage-use-azcopy-files.md)

- [Adatok áthelyezése az AzCopy és az Amazon S3 gyűjtő](storage-use-azcopy-s3.md)

## <a name="use-azcopy-in-a-script"></a>Az AzCopy használata a parancsfájl

Az AzCopy az idő múlásával [letöltési hivatkozás](#download-and-install-azcopy) AzCopy új verzióinak mutasson. A szkript letölti az AzCopy, ha a parancsfájl előfordulhat, hogy működését gátolja, ha az AzCopy egy újabb verzióját módosítja a parancsfájl attól függ, hogy szolgáltatásokat. 

Ezek a problémák elkerülése érdekében az aktuális verzióra, az AzCopy statikus (nem változó) hivatkozás beszerzése. Ezzel a módszerrel a szkript letölti az AzCopy pontos ugyanazon verzióját minden alkalommal, amikor azt futtató.

A hivatkozás beszerzéséhez a következő parancs futtatásával:

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Linux esetén `--strip-components=1` a a `tar` parancs eltávolítja a legfelső szintű mappát, amely a verzió nevét tartalmazza, és ehelyett kibontja a bináris fájlt közvetlenül az aktuális mappába. Ez lehetővé teszi, hogy az új verzióval frissíteni kell a parancsfájl `azcopy` csak frissítésével a `wget` URL-CÍMÉT.

Ez a parancs kimenete az URL-cím jelenik meg. A szkript ezután letöltheti az AzCopy használatával URL-CÍMRE.

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>Az AzCopy használata a Storage Explorerben

Ha szeretné kihasználni az AzCopy teljesítmény előnyeit, de szeretné használni a parancssor helyett a Storage Explorer használatával kommunikálhat a fájlokat, majd engedélyezze az AzCopy a Storage Explorerben. 

A Storage Explorerben válassza **előzetes**->**használható AzCopy továbbfejlesztett Blob feltöltése és letöltése**.

![Az Azure Storage Explorer egy átviteli motorként AzCopy engedélyezése](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Nem engedélyezi ezt a beállítást, ha engedélyezte a tárfiók a hierarchikus névtér kell. Mivel a Storage Explorer automatikusan használja az AzCopy a storage-fiókok hierarchikus névtérrel rendelkező ez.  

Storage Explorer használja a fiókkulcs műveleteket hajtson végre, így miután bejelentkezett a Storage Explorer, nem kell további engedélyezési hitelesítő adatait.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Az előző verzió, az AzCopy használata

Ha korábbi verzióját használja, az AzCopy (AzCopy v8.1) van szüksége, tekintse át az alábbi hivatkozásokat:

- [Az AzCopy Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy linuxon (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurálja, optimalizálhat és AzCopy hibaelhárítása

Lásd: [konfigurálása, optimalizálása és AzCopy hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>További lépések

Ha kérdése, problémák vagy általános visszajelzést, küldje el őket [a Githubon](https://github.com/Azure/azure-storage-azcopy) lapot.
