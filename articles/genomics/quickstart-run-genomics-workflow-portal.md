---
title: 'Gyors útmutató: munkafolyamat futtatása – Microsoft Genomics'
description: Ez a rövid útmutató bemutatja, hogyan tölthet be bementi adatokat az Azure Blob Storage-ba, illetve hogyan futtathat munkafolyamatokat a Microsoft Genomics szolgáltatással.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.openlocfilehash: 05b94ca9bd14392bad5288882a80f5c75590ef7b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931794"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Első lépések: Munkafolyamat futtatása a Microsoft Genomics szolgáltatásban

Ebben a rövid útmutatóban egy Azure Blob Storage-fiókba tölti fel a bemeneti adatokat, és a Microsoft Genomics szolgáltatáson keresztül futtatja a munkafolyamatot a Python genomikai ügyfél használatával. A Microsoft Genomics egy méretezhető, biztonságos másodlagos elemzési szolgáltatás, amely képes a genom gyors feldolgozására, és nyersolvasásokból kiindulva rendezett beolvasásokat és változóhívásokat hoz létre. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Python 2.7.12 +](https://www.python.org/downloads/release/python-2714/), `pip` telepítve és `python` a rendszerútvonalon. A Microsoft Genomics ügyfél nem kompatibilis a Python 3 rendszerrel. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Előkészületek: Microsoft Genomics-fiók létrehozása az Azure Portalon

Microsoft Genomics-fiók létrehozásához navigáljon a Azure Portal a [genomikai fiók létrehozásához](https://portal.azure.com/#create/Microsoft.Genomics) . Ha még nem rendelkezik Azure-előfizetéssel, a Microsoft Genomics-fiók létrehozása előtt hozzon létre egyet. 

![Microsoft Genomics on Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics on Azure Portal")

Konfigurálja Genomics-fiókját az alábbi információkkal az előző képen látható módon. 

 |**Beállítás**          |  **Ajánlott érték**  | **Mező leírása** |
 |:-------------       |:-------------         |:----------            |
 |Előfizetést         | Az Ön előfizetésének neve|Ez az Azure-szolgáltatásokhoz tartozó számlázási egység – Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.azure.com/Subscriptions) ismertető cikket. |      
 |Erőforráscsoport       | MyResourceGroup       |  Az erőforráscsoportok használatával több Azure-erőforrást (Storage-fiók, Genomics-fiók stb.) rendezhet egy csoportba a könnyebb kezelhetőség érdekében. További információkért lásd: [Erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
 |Fióknév         | MyGenomicsAccount     |Válasszon egyedi fiókazonosítót. Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
 |Földrajzi egység                   | USA 2. nyugati régiója                    |    A szolgáltatás a következő régiókban érhető el: az USA 2. nyugati régiója, Nyugat-Európa, és Délkelet-Ázsia |

A felső menüsorban az **értesítések** lehetőségre kattintva figyelheti a telepítési folyamatot.

![Értesítések](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Értesítések")

További információ a Microsoft Genomicsről: [Mi az Microsoft Genomics?](overview-what-is-genomics.md)

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Előkészületek: A Microsoft Genomics Python-kliens telepítése

Telepítenie kell a Pythont és a Microsoft Genomics Python-ügyfelet a helyi környezetben. 

### <a name="install-python"></a>Telepítse a Pythont

A Microsoft Genomics Python-ügyfél kompatibilis a Python 2.7.12 vagy újabb 2.7. xx-es verziójával. a 2.7.14 a javasolt verzió. A letöltés [itt](https://www.python.org/downloads/release/python-2714/) található. 

> [!IMPORTANT]
> A Python 3. x nem kompatibilis a Python 2.7. xx-mel.  Az MSGen egy Python 2.7-alkalmazás. Ha az MSGen alkalmazást futtatja, ügyeljen arra, hogy az aktív Python-környezete 2.7.xx-es Python-verziót használjon. Ha az MSGen alkalmazást egy 3.x-es Python-verzióval használja, előfordulhat, hogy hibák jelentkeznek.

### <a name="install-the-microsoft-genomics-client"></a>A Microsoft Genomics-kliens telepítése

A Python `pip` segítségével telepítse a Microsoft Genomics ügyfél `msgen`. Az alábbi utasítások feltételezik, hogy a Python már telepítve van a rendszer elérési útján. Ha problémái vannak a `pip` telepítésével kapcsolatban, akkor a rendszer elérési útjához hozzá kell adnia a Pythont és a szkriptek almappáját.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Ha nem szeretné a `msgen` rendszerszintű bináris fájlként telepíteni, és módosítja a rendszerszintű Python-csomagokat, használja a `–-user` jelzőt a `pip`használatával.
Ha a csomag alapú telepítést vagy a setup.py fájlt használja, azzal minden szükséges csomagot telepít. Ellenkező esetben a `msgen` alapvető szükséges csomagjai 

 * [Azure-tároló](https://pypi.python.org/pypi/azure-storage). 
 * [Kérések](https://pypi.python.org/pypi/requests). 

Az említett csomagokat `pip`, `easy_install` vagy szabványos `setup.py` eljárásokkal is telepítheti. 

### <a name="test-the-microsoft-genomics-client"></a>A Microsoft Genomics-kliens tesztelése
A Microsoft Genomics-ügyfél teszteléséhez töltse le a konfigurációs fájlt a genomikai fiókjából. A Azure Portalban navigáljon a genomikai fiókjához a bal felső sarokban lévő **összes szolgáltatás** kiválasztásával, majd a genomikai fiókok keresésével és kiválasztásával.

![Microsoft Genomics keresése Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Microsoft Genomics keresése Azure Portal")

Válassza ki az imént létrehozott genomikai fiókot, navigáljon a **hozzáférési kulcsok**elemre, és töltse le a konfigurációs fájlt.

![Konfigurációs fájl letöltése Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Konfigurációs fájl letöltése Microsoft Genomics")

Az alábbi paranccsal próbálja ki, hogy a Microsoft Genomics Python-kliens működik-e:

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Microsoft Azure Storage fiók létrehozása 
A Microsoft Genomics szolgáltatás a bemeneteket az Azure Storage-fiókban tárolt blokkblobok formájában várja. Emellett a kimeneti fájlokat is blokkblobok formájában írja a felhasználó által meghatározott Azure Storage-fiókban lévő tárolóba. A bemenetek és kimenetek különböző tárfiókokban is lehetnek.
Ha az adatok már egy Azure Storage-fiókban vannak, csak azt kell ellenőriznie, hogy a fiók ugyanazon a helyen található-e, mint a Genomics-fiók. Ellenkező esetben a Microsoft Genomics szolgáltatás futtatásakor a kimenő forgalomért járó költségek merülnek fel. Ha még nem rendelkezik Azure Storage-fiókkal, létre kell hoznia egyet, és fel kell töltenie az adatait. További információt az Azure Storage-fiókokról [itt](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)talál, beleértve a Storage-fiókokat és az általa biztosított szolgáltatásokat. Azure Storage-fiók létrehozásához navigáljon a Azure Portal a [Storage-fiók létrehozásához](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) .  

![Storage-fiók létrehozása lap](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Storage-fiók létrehozása lap")

Konfigurálja a Storage-fiókját az alábbi információkkal az előző képen látható módon. Használjon egy Storage-fiókhoz a legtöbb szabványos beállítást, amely csak azt határozza meg, hogy a fiók BlobStorage, nem általános célú. A le- és feltöltés a blobtárolók esetében 2–5-ször gyorsabb.  Az alapértelmezett üzemi modell (Azure Resource Manager) ajánlott.  

 |**Beállítás**          |  **Ajánlott érték**  | **Mező leírása** |
 |:-------------------------       |:-------------         |:----------            |
 |Előfizetést         | Az Azure-előfizetése |Az előfizetései részleteivel kapcsolatban lásd: [Előfizetések](https://account.azure.com/Subscriptions) |      
 |Erőforráscsoport       | MyResourceGroup       |  A genomikai fiókkal megegyező erőforráscsoportot is kiválaszthatja. Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd: [elnevezési szabályok](/azure/architecture/best-practices/resource-naming) |
 |Storage account name (Tárfiók neve)         | MyStorageAccount     |Válasszon egyedi fiókazonosítót. Érvényes nevek: [elnevezési szabályok](/azure/architecture/best-practices/resource-naming) |
 |Földrajzi egység                  | USA 2. nyugati régiója                  | Használja ugyanazt a helyet, mint a genomikai fiók helye, a kimenő forgalom költségeinek csökkentése és a késés csökkentése érdekében.  | 
 |Teljesítmény                  | Standard                   | Az alapértelmezett beállítás a standard. A standard és a Premium Storage-fiókokkal kapcsolatos további részletekért lásd: [a Microsoft Azure Storage bemutatása](https://docs.microsoft.com/azure/storage/common/storage-introduction)    |
 |Fióktípus       | BlobStorage       |  A le- és feltöltés a blobtárolók esetében 2–5-ször gyorsabb az általános célú fiókokhoz képest. |
 |Replikáció                  | Helyileg redundáns tárolás                  | A helyileg redundáns tárolással a rendszer abban a régióban lévő adatközpontba replikálja az adatokat, amelyben a tárfiókot létrehozták. További információ: [Azure Storage replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy)    |
 |Elérési szint                  | Gyakori                   | A Gyakori hozzáférés a tárfiókban tárolt objektumok gyakoribb elérésére utal.    |

Ezután válassza a **felülvizsgálat + létrehozás** lehetőséget a Storage-fiók létrehozásához. A genomikai fiók létrehozása során a felső menüsorban az **értesítések** lehetőségre kattintva figyelheti a telepítési folyamatot. 

## <a name="upload-input-data-to-your-storage-account"></a>Bemeneti adatok feltöltése a Storage-fiókba

A Microsoft Genomics szolgáltatás bemeneti fájlként várja a párosított befejezési olvasási (fastq-vagy BAM-) fájlokat. Feltöltheti saját adatait, vagy kipróbálhatja a szolgáltatást a nyilvánosan elérhető mintaadatokkal. Ha a nyilvánosan elérhető mintaadatokat szeretné használni, az alábbi helyen érheti el azokat:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

A tárfiókban létre kell hoznia egy blobtárolót a bemeneti adatok számára, valamint egy másikat a kimeneti adatok számára.  Töltse fel a bemeneti adatokat a bemeneti blobtárolóba. Ehhez több eszközt is használhat, beleértve a [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), a [BlobPorter](https://github.com/Azure/blobporter)vagy a [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Munkafolyamat futtatása a Microsoft Genomics szolgáltatással a Python-kliens használatával | Microsoft Docs 

Ha a Microsoft Genomics szolgáltatáson keresztül szeretne futtatni egy munkafolyamatot, szerkessze a *config. txt* fájlt, és adja meg a bemeneti és kimeneti tárolót az adatokhoz.
Nyissa meg a genomikai fiókjából letöltött *config. txt* fájlt. A megadható fejezetek az előfizetési kulcs és az alsó hat elem, a Storage-fiók neve, a kulcs és a tároló neve mind a bemenet, mind a kimenet számára. Ezeket az információkat a Azure Portal navigálva érheti el a Storage-fiók **kulcsainak eléréséhez** , vagy közvetlenül a Azure Storage Explorer.  

![Genomikai konfiguráció](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Genomikai konfiguráció")

Ha a GATK4-t szeretné futtatni, állítsa a `process_name` paramétert `gatk4`re.

Alapértelmezés szerint a genomikai szolgáltatás a (vagy Ha gVCF kimenetet szeretne, és nem egy VCF kimenetet (amely egyenértékű `-emitRefConfidence` a GATK 3. x-es és a GATK 4. x-ben `emit-ref-confidence`), adja hozzá a `emit_ref_confidence` paramétert a *config. txt* fájlhoz, és állítsa a `gvcf`értékre, ahogyan az előző ábrán látható.  Ha vissza szeretné állítani a VCF kimenetét, távolítsa el a *config. txt* fájlból, vagy állítsa a `emit_ref_confidence` paramétert `none`re. 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Munkafolyamat elküldése a Microsoft Genomics szolgáltatásba a Microsoft Genomics-kliensen keresztül

A Microsoft Genomics Python-kliensen keresztül a munkafolyamatot az alábbi parancs használatával küldheti el:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

A munkafolyamatok állapotát a következő paranccsal tekintheti meg: 
```python
msgen list -f c:\temp\config.txt 
```

A munkafolyamat befejezése után megtekintheti az Azure Storage-fiókban lévő kimeneti fájlokat a konfigurált kimeneti tárolóban. 

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben a minta bemeneti adatokat feltöltötte az Azure Storage-ba, és elküldte a munkafolyamatot a Microsoft Genomics szolgáltatásnak a `msgen` Python-ügyféllel. Ha bővebb információra van szüksége más, a Microsoft Genomics szolgáltatásban használható bemeneti fájltípusokkal kapcsolatban, tekintse meg a következő lapokat: [párosított FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Több FASTQ vagy BAM](quickstart-input-multiple.md). Ezt az oktatóanyagot megtalálhatja az [Azure Notebooks-oktatóanyagok](https://aka.ms/genomicsnotebook) között is.
