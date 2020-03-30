---
title: 'Rövid útmutató: Munkafolyamat futtatása – Microsoft Genomics'
description: Ez a rövid útmutató bemutatja, hogyan tölthet be bementi adatokat az Azure Blob Storage-ba, illetve hogyan futtathat munkafolyamatokat a Microsoft Genomics szolgáltatással.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.openlocfilehash: 05b94ca9bd14392bad5288882a80f5c75590ef7b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76931794"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Rövid útmutató: Munkafolyamat futtatása a Microsoft Genomics szolgáltatással

Ebben a rövid útmutatóban feltölti a bemeneti adatokat egy Azure Blob-tárfiókba, és a Python Genomics ügyfél használatával futtat egy munkafolyamatot a Microsoft Genomics szolgáltatáson keresztül. A Microsoft Genomics egy méretezhető, biztonságos másodlagos elemzési szolgáltatás, amely képes a genom gyors feldolgozására, és nyersolvasásokból kiindulva rendezett beolvasásokat és változóhívásokat hoz létre. 

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Python 2.7.12+](https://www.python.org/downloads/release/python-2714/) `pip` , telepített, és `python` a rendszer elérési útját. A Microsoft Genomics kliens nem kompatibilis a Python 3-mal. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Előkészületek: Microsoft Genomics-fiók létrehozása az Azure Portalon

Microsoft Genomics-fiók létrehozásához keresse meg [genomikai fiók létrehozása](https://portal.azure.com/#create/Microsoft.Genomics) az Azure Portalon. Ha még nem rendelkezik Azure-előfizetéssel, a Microsoft Genomics-fiók létrehozása előtt hozzon létre egyet. 

![Microsoft Genomics az Azure portalon](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics az Azure portalon")

Konfigurálja Genomics-fiókját az alábbi információkkal az előző képen látható módon. 

 |**Beállítás**          |  **Ajánlott érték**  | **Mező leírása** |
 |:-------------       |:-------------         |:----------            |
 |Előfizetés         | Az Ön előfizetésének neve|Ez az Azure-szolgáltatásokhoz tartozó számlázási egység – Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.azure.com/Subscriptions) ismertető cikket. |      
 |Erőforráscsoport       | MyResourceGroup       |  Az erőforráscsoportok használatával több Azure-erőforrást (Storage-fiók, Genomics-fiók stb.) rendezhet egy csoportba a könnyebb kezelhetőség érdekében. További információkért lásd: [Erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
 |Fióknév         | MyGenomicsAccount     |Válasszon egyedi fiókazonosítót. Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
 |Hely                   | USA nyugati régiója, 2.                    |    A szolgáltatás a következő régiókban érhető el: az USA 2. nyugati régiója, Nyugat-Európa, és Délkelet-Ázsia |

A felső menüsorban az **Értesítések** lehetőséget választva figyelheti a telepítési folyamatot.

![Értesítések](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Értesítések")

A Microsoft Genomics szolgáltatásról a [Mi a Microsoft Genomics?](overview-what-is-genomics.md)

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Előkészületek: A Microsoft Genomics Python-kliens telepítése

Telepítenie kell a Python és a Microsoft Genomics Python-ügyfél a helyi környezetben. 

### <a name="install-python"></a>Telepítse a Pythont

A Microsoft Genomics Python kliens kompatibilis a Python 2.7.12 vagy egy későbbi 2.7.xx verzióval. 2.7.14 a javasolt változat. A letöltés [itt](https://www.python.org/downloads/release/python-2714/) található. 

> [!IMPORTANT]
> A Python 3.x nem kompatibilis a Python 2.7.xx programmal.  Az MSGen egy Python 2.7-alkalmazás. Ha az MSGen alkalmazást futtatja, ügyeljen arra, hogy az aktív Python-környezete 2.7.xx-es Python-verziót használjon. Ha az MSGen alkalmazást egy 3.x-es Python-verzióval használja, előfordulhat, hogy hibák jelentkeznek.

### <a name="install-the-microsoft-genomics-client"></a>A Microsoft Genomics-kliens telepítése

A `pip` Python segítségével telepítse a `msgen`Microsoft Genomics klienst. Az alábbi utasítások feltételezik, hogy a Python már telepítve van a rendszer elérési útján. Ha problémái vannak `pip` a telepítés nem ismeri fel, hozzá kell adnia a Python és a parancsfájlok almappát a rendszer elérési útját.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Ha nem szeretné rendszerszintű `msgen` bináris fájlként telepíteni és rendszerszintű Python-csomagokat módosítani, használja a jelzőt a `–-user` segítségével. `pip`
Ha a csomag alapú telepítést vagy a setup.py fájlt használja, azzal minden szükséges csomagot telepít. Ellenkező esetben az `msgen` alapvetően szükséges csomagok 

 * [Azure-storage .](https://pypi.python.org/pypi/azure-storage) 
 * [Kérések](https://pypi.python.org/pypi/requests). 

Az említett csomagokat `pip`, `easy_install` vagy szabványos `setup.py` eljárásokkal is telepítheti. 

### <a name="test-the-microsoft-genomics-client"></a>A Microsoft Genomics-kliens tesztelése
A Microsoft Genomics kliens teszteléséhez töltse le a konfigurációs fájlt a Genomics-fiókjából. Az Azure Portalon keresse meg a Genomics-fiókot a bal felső sarokban az **Összes szolgáltatás** elemre kattintva, majd keresse meg és válassza ki a Genomics-fiókokat.

![A Microsoft Genomics megkeresése az Azure portalon](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "A Microsoft Genomics megkeresése az Azure portalon")

Válassza ki az imént készített Genomics fiókot, keresse meg az **Access Keys**elemet, és töltse le a konfigurációs fájlt.

![A konfigurációs fájl letöltése a Microsoft Genomics programból](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "A konfigurációs fájl letöltése a Microsoft Genomics programból")

Az alábbi paranccsal próbálja ki, hogy a Microsoft Genomics Python-kliens működik-e:

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Microsoft Azure Storage-fiók létrehozása 
A Microsoft Genomics szolgáltatás a bemeneteket az Azure Storage-fiókban tárolt blokkblobok formájában várja. Emellett a kimeneti fájlokat is blokkblobok formájában írja a felhasználó által meghatározott Azure Storage-fiókban lévő tárolóba. A bemenetek és kimenetek különböző tárfiókokban is lehetnek.
Ha az adatok már egy Azure Storage-fiókban vannak, csak azt kell ellenőriznie, hogy a fiók ugyanazon a helyen található-e, mint a Genomics-fiók. Ellenkező esetben a Microsoft Genomics szolgáltatás futtatásakor a kimenő forgalom díjai merülnek fel. Ha még nem rendelkezik Azure-tárfiókkal, létre kell hoznia egyet, és fel kell töltenie az adatokat. Az Azure storage-fiókokról [here](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)itt talál további információt, beleértve azt is, hogy mi az a tárfiók, és milyen szolgáltatásokat nyújt. Azure-tárfiók létrehozásához keresse meg a [tárfiók létrehozása](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) az Azure Portalon.  

![Tárfiók létrehozása lap](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Tárfiók létrehozása lap")

Konfigurálja a tárfiókot a következő információkkal, ahogy az előző képen látható. Használja a tárfiók szabványos beállításainak többségét, és csak azt adja meg, hogy a fiók BlobStorage, nem általános célú. A le- és feltöltés a blobtárolók esetében 2–5-ször gyorsabb.  Az alapértelmezett üzembe helyezési modell, az Azure Resource Manager, ajánlott.  

 |**Beállítás**          |  **Ajánlott érték**  | **Mező leírása** |
 |:-------------------------       |:-------------         |:----------            |
 |Előfizetés         | Az Azure-előfizetése |Az előfizetései részleteivel kapcsolatban lásd: [Előfizetések](https://account.azure.com/Subscriptions) |      
 |Erőforráscsoport       | MyResourceGroup       |  Ugyanazt az erőforráscsoportot választhatja ki, mint a Genomics-fiók. Az érvényes erőforráscsoportnevekről az [Elnevezési szabályok](/azure/architecture/best-practices/resource-naming) |
 |Storage account name (Tárfiók neve)         | MyStorageAccount     |Válasszon egyedi fiókazonosítót. Az érvényes nevekről az [Elnevezési szabályok](/azure/architecture/best-practices/resource-naming) |
 |Hely                  | USA nyugati régiója, 2.                  | Használja ugyanazt a helyet, mint a Genomics-fiók helye, a kimenő forgalom díjainak csökkentése és a késés csökkentése érdekében.  | 
 |Teljesítmény                  | Standard                   | Az alapértelmezett beállítás a standard. A standard és prémium szintű tárfiókokról a [Bevezetés a Microsoft Azure storage-ba című](https://docs.microsoft.com/azure/storage/common/storage-introduction) témakörben talál további részleteket.    |
 |Fióktípus       | BlobStorage tároló       |  A le- és feltöltés a blobtárolók esetében 2–5-ször gyorsabb az általános célú fiókokhoz képest. |
 |Replikáció                  | Helyileg redundáns tárolás                  | A helyileg redundáns tárolással a rendszer abban a régióban lévő adatközpontba replikálja az adatokat, amelyben a tárfiókot létrehozták. További információ: [Azure Storage replication](https://docs.microsoft.com/azure/storage/common/storage-redundancy)    |
 |Hozzáférési szint                  | Gyakori                   | A Gyakori hozzáférés a tárfiókban tárolt objektumok gyakoribb elérésére utal.    |

Ezután válassza **a Véleményezés + létrehozás** lehetőséget a tárfiók létrehozásához. Agenomics-fiók létrehozásához ugyanúgy kiválaszthatja az **Értesítések** lehetőséget a felső menüsorban a telepítési folyamat figyeléséhez. 

## <a name="upload-input-data-to-your-storage-account"></a>Bemeneti adatok feltöltése a Storage-fiókba

A Microsoft Genomics szolgáltatás a párosított végolvasásokat (fastq vagy bam fájlokat) bemeneti fájlként várja. Feltöltheti saját adatait, vagy kipróbálhatja a szolgáltatást a nyilvánosan elérhető mintaadatokkal. Ha a nyilvánosan elérhető mintaadatokat szeretné használni, az alábbi helyen érheti el azokat:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

A tárfiókban létre kell hoznia egy blobtárolót a bemeneti adatok számára, valamint egy másikat a kimeneti adatok számára.  Töltse fel a bemeneti adatokat a bemeneti blobtárolóba. Ehhez különböző eszközök használhatók, például [a Microsoft Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)a [BlobPorter](https://github.com/Azure/blobporter)vagy [az AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Munkafolyamat futtatása a Microsoft Genomics szolgáltatással a Python-kliens használatával | Microsoft Docs 

Munkafolyamat futtatásához a Microsoft Genomics szolgáltatáson keresztül, szerkessze a *config.txt* fájlt az adatok bemeneti és kimeneti tárolótárolójának megadásához.
Nyissa meg a Genomics-fiókjából letöltött *config.txt* fájlt. A megadandó szakaszok az előfizetési kulcs és a hat elem alján, a tárfiók neve, kulcs és a tároló neve mind a bemeneti és kimeneti. Ezeket az információkat úgy találhatja meg, hogy az Azure Portalon a tárfiók **hoz való Access-kulcsokhoz** navigál, vagy közvetlenül az Azure Storage Explorerből.  

![Genomikai konfektikum](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Genomikai konfektikum")

Ha futtatni szeretné a GATK4-et, állítsa a `process_name` paramétert a-ra. `gatk4`

Alapértelmezés szerint a Genomics szolgáltatás vcf fájlokat ad ki. Ha vVCF-kimenetet szeretne a VCF-kimenet `-emitRefConfidence` helyett (ami egyenértékű a `emit-ref-confidence` GATK 3.x és `emit_ref_confidence` a GATK 4.x értékével), adja hozzá a paramétert a *config.txt* fájlhoz, és állítsa be a beállításra, `gvcf`ahogy az az előző ábrán is látható.  Ha vissza szeretne állítani VCF-kimenetre, távolítsa el azt a `emit_ref_confidence` *config.txt* fájlból, vagy állítsa a paramétert a-ra. `none` 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Munkafolyamat elküldése a Microsoft Genomics szolgáltatásba a Microsoft Genomics-kliensen keresztül

A Microsoft Genomics Python-kliensen keresztül a munkafolyamatot az alábbi parancs használatával küldheti el:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

A munkafolyamatok állapotát a következő paranccsal tekintheti meg: 
```python
msgen list -f c:\temp\config.txt 
```

Miután a munkafolyamat befejeződött, megtekintheti a kimeneti fájlokat az Azure storage-fiókban a beállított kimeneti tárolóban. 

## <a name="next-steps"></a>További lépések
Ebben a cikkben feltöltötte a mintabemeneti adatokat az Azure storage-ba, és egy munkafolyamatot küldött a Microsoft Genomics szolgáltatásnak a `msgen` Python-ügyfélen keresztül. Ha többet szeretne megtudni a Microsoft Genomics szolgáltatással használható egyéb bemeneti fájltípusokról, olvassa el a következő oldalakat: [párosított FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ vagy BAM](quickstart-input-multiple.md). Ezt az oktatóanyagot megtalálhatja az [Azure Notebooks-oktatóanyagok](https://aka.ms/genomicsnotebook) között is.
