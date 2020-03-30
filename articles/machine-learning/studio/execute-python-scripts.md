---
title: Python-szkriptek végrehajtása
titleSuffix: ML Studio (classic) - Azure
description: Ismerje meg, hogyan használhatja a Python-parancsfájl-modult a Python-kód használatához a Machine Learning Studio (klasszikus) kísérletekben és a webes szolgáltatásokban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218083"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Python-gépi tanulási parancsfájlok végrehajtása az Azure Machine Learning Studio-ban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A Python értékes eszköz számos adattudós eszközládájában. A tipikus gépi tanulási munkafolyamatok minden szakaszában használatos, beleértve az adatfeltárást, a funkciók kinyerését, a modellbetanítást és -érvényesítést, valamint az üzembe helyezést.

Ez a cikk ismerteti, hogyan használhatja a Python-parancsfájl végrehajtása modul használata Python-kódot az Azure Machine Learning Studio (klasszikus) kísérletek és a webes szolgáltatások.

## <a name="using-the-execute-python-script-module"></a>A Python-parancsfájl végrehajtása modul használata

Az elsődleges felület a Python a Studio (klasszikus) a [Python-parancsfájl végrehajtása][execute-python-script] modulon keresztül. Legfeljebb három bemenetet fogad el, és legfeljebb két kimenetet hoz létre, hasonlóan az [R-parancsfájl végrehajtása][execute-r-script] modulhoz. A Python-kódot egy speciálisan elnevezett belépési `azureml_main`pont nevű függvényen keresztül kell beírni a paramétermezőbe.

![Python-parancsfájl-modul végrehajtása](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Minta python kód modul paraméter doboz](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Bemeneti paraméterek

A Python-modul bemenetei Pandas DataFrames néven vannak elérhetővé téve. A `azureml_main` függvény legfeljebb két választható Pandas dataframe paramétert fogad el paraméterként.

A bemeneti portok és a függvényparaméterek közötti leképezés helymeghatározás:

- Az első csatlakoztatott bemeneti port a függvény első paraméteréhez van rendelve.
- A második bemenet (ha csatlakoztatva van) a függvény második paraméteréhez van rendelve.
- A harmadik bemenet [további Python-modulok importálására](#import-modules)szolgál.

Az alábbiakban részletesebb szemantika látható arról, hogy `azureml_main` a bemeneti portok hogyan kerülnek leképezve a függvény paramétereihez.

![Bemeneti portkonfigurációk táblázata és az eredményül kapott Python-aláírás](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Kimeneti visszatérési értékek

A `azureml_main` függvénynek egyetlen Pandas DataFrame-et kell visszaadnia egy [Python-sorrendben,](https://docs.python.org/2/c-api/sequence.html) például egy törzsben, listában vagy NumPy tömbben. A szekvencia első eleme visszakerül a modul első kimeneti portjára. A modul második kimeneti portja [vizualizációkhoz](#visualizations) használatos, és nem igényel visszatérési értéket. Ez a séma az alábbiakban látható.

![Bemeneti portok hozzárendelése paraméterekhez és visszatérési érték a kimeneti porthoz](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Bemeneti és kimeneti adattípusok fordítása

A stúdió adatkészletei nem egyeznek meg a Panda DataFrames adatkészletekkel. Ennek eredményeképpen a Studio bemeneti adatkészletei (klasszikus) Pandas DataFrame-re, a kimeneti DataFrames-ek pedig Studio (klasszikus) adatkészletekké konvertálódnak vissza. Az átalakítási folyamat során a következő fordítások is végrehajtásra kerülnek:

 **Python-adattípus** | **Stúdiófordítási eljárás** |
| --- | --- |
| Karakterláncok és numerikus| Fordítás ahogy van |
| Pandák 'NA' | "Hiányzó érték" néven lefordítva |
| Tárgymutató-vektorok | Nem támogatott* |
| Nem karakterláncoszlopnevek | Behívás `str` oszlopnevek |
| Ismétlődő oszlopnevek | Adja hozzá a numerikus utótagot: (1), (2), (3) és így tovább.

**A Python függvény ben lévő összes bemeneti adatkeret nek mindig van egy 64 bites numerikus indexe 0 és a sorok száma mínusz 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Meglévő Python-parancsfájlmodulok importálása

A Python végrehajtásához használt háttérrendszer az [Anaconda,](https://www.anaconda.com/distribution/)egy széles körben használt tudományos Python-disztribúción alapul. Közel 200 az adatközpontú számítási feladatokban használt leggyakoribb Python-csomagok. A Studio (klasszikus) jelenleg nem támogatja a külső kódtárak telepítéséhez és kezeléséhez használt csomagkezelő rendszerek, például a Pip vagy a Conda használatát.  Ha további könyvtárakat szeretne beépíteni, használja a következő forgatókönyvet útmutatóként.

A meglévő Python-parancsfájlok studio (klasszikus) kísérletekbe való beépítése gyakori használati eset. A [Python-parancsfájl végrehajtása][execute-python-script] modul elfogadja a zip-fájlt, amely python-modulokat tartalmaz a harmadik bemeneti porton. A fájlt a végrehajtási keretrendszer futásidőben kicsomagolja, és a tartalom hozzáadódik a Python-értelmező könyvtári elérési útjához. A `azureml_main` belépési pont funkció ezután közvetlenül importálhatja ezeket a modulokat. 

Vegyük például azt a fájlt, Hello.py egy egyszerű "Hello, World" függvényt tartalmazó fájl.

![Felhasználó által definiált függvény Hello.py fájlban](./media/execute-python-scripts/figure4.png)

Ezután létrehozunk egy hello.zip fájlt, amely Hello.py tartalmaz:

![A felhasználó által definiált Python-kódot tartalmazó zip-fájl](./media/execute-python-scripts/figure5.png)

Töltse fel a zip fájlt adatkészletként a Studio (klasszikus). Ezután hozzon létre és futtasson egy kísérletet, amely a Python-kódot használja a Hello.zip fájlban, csatolva azt a **Python-parancsfájl végrehajtása** modul harmadik bemeneti portjához, ahogy az az alábbi képen látható.

![Mintakísérlet a Hello.zip-tel egy Python-parancsfájl-modul beviteleként](./media/execute-python-scripts/figure6a.png)

![Felhasználó által definiált Python-kód zip fájlként feltöltve](./media/execute-python-scripts/figure6b.png)

A modul kimenete azt mutatja, hogy a zip `print_hello` fájl ki van csomagolva, és hogy a függvény fut.

![A felhasználó által definiált függvényt megjelenítő modulkimenet](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Az Azure Storage blobok elérése

Az alábbi lépésekkel érheti el az Azure Blob Storage-fiókban tárolt adatokat:

1. Töltse le az [Azure Blob Storage-csomagot a Python helyileg.](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip)
1. Töltse fel a zip fájlt a Studio (klasszikus) munkaterületére adatkészletként.
1. A BlobService-objektum létrehozása`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. **A biztonságos átvitel** letiltása szükséges a Tároló **konfigurációja** beállítás lapon

![Biztonságos átvitel letiltása szükséges az Azure Portalon](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Python-parancsfájlok üzembe építése

A pontozási kísérletben használt [Python-parancsfájl-modulok végrehajtása][execute-python-script] a webszolgáltatásként való közzétételkor lesz meghívva. Például az alábbi képen látható egy pontozási kísérlet, amely tartalmazza a kódot, hogy értékelje ki egy Python-kifejezés.

![Webszolgáltatás stúdiómunkaterülete](./media/execute-python-scripts/figure3a.png)

![Python Pandas kifejezés](./media/execute-python-scripts/python-script-with-python-pandas.png)

A kísérletből létrehozott webszolgáltatás a következő műveleteket végrehajtja:

1. Python-kifejezés bevitele (karakterláncként)
1. A Python-kifejezés küldése a Python-értelmezőnek
1. A kifejezést és a kiértékelt eredményt is tartalmazó táblát ad eredményül.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Képi megjelenítések közös

A MatplotLib használatával létrehozott telkeket a [Python-parancsfájl végrehajtása][execute-python-script]adja vissza. A telkek et azonban a program nem irányítja át automatikusan a képekre, ahogy az R használatakor vannak. Tehát a felhasználónak explicit módon mentenie kell a telkeket a PNG fájlokba.

A MatplotLib-ból származó képek létrehozásához a következő lépéseket kell tennie:

1. Váltson a háttérrendszer "AGG" értékre az alapértelmezett Qt-alapú renderelőről.
1. Hozzon létre egy új alakzatobjektumot.
1. Szerezd meg a tengelyt, és generálj bele minden telket.
1. Mentse az ábrát PNG-fájlba.

Ezt a folyamatot a következő képek szemléltetik, amelyek a Pandák scatter_matrix függvényének használatával szórásos nyomtatási mátrixot hoznak létre.

![Kód a MatplotLib figurák képekre mentéséhez](./media/execute-python-scripts/figure-v1-8.png)

![Kattintson a Visualize elemre egy Python-parancsfájl végrehajtása modulon az ábrák megtekintéséhez](./media/execute-python-scripts/figure-v2-9a.png)

![Mintakísérlet telkekmegjelenítése Python-kód használatával](./media/execute-python-scripts/figure-v2-9b.png)

Több számjegyet is vissza lehet adni, ha különböző képekre menti őket. A Studio (klasszikus) futásidejű felveszi az összes képet, és összefűzi őket a vizualizációhoz.

## <a name="advanced-examples"></a>Speciális példák

A Studio (klasszikus) környezetben telepített Anaconda környezet olyan gyakori csomagokat tartalmaz, mint a NumPy, a SciPy és a Scikits-Learn. Ezek a csomagok hatékonyan használhatók a gépi tanulási folyamat adatfeldolgozásához.

Például a következő kísérlet és a szkript szemlélteti az együttes tanulók használatát a Scikits-Learn-ben egy adatkészlet jellemzőfontossági pontszámainak kiszámításához. A pontszámok segítségével felügyelt funkciókiválasztása, mielőtt egy másik modellbe adagolni.

Itt van a Python függvény kiszámításához használt fontossági pontszámok és a funkciók sorrendje alapján a pontszámok:

![Funkció a funkciók rangsorolására pontszámok szerint](./media/execute-python-scripts/figure8.png)

A következő kísérlet ezután kiszámítja és visszaadja a "Pima indian diabetes" adatkészlet "Pima indian diabetes" adatkészletének számos szolgáltatását (klasszikus):

![Kísérletezzen a Pima Indian Diabetes adatkészlet funkcióinak rangsorolására a Python használatával](./media/execute-python-scripts/figure9a.png)

![A Python-parancsfájl végrehajtása modul kimenetének vizualizálása](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Korlátozások

A [Python-parancsfájl végrehajtása][execute-python-script] modul jelenleg a következő korlátozásokkal rendelkezik:

### <a name="sandboxed-execution"></a>Sandboxed végrehajtás

A Python-futásidejű jelenleg sandboxed, és nem teszi lehetővé a hozzáférést a hálózathoz vagy a helyi fájlrendszer állandó módon. A program a modul befejeződése után elkülöníti és törli az összes helyileg mentett fájlt. A Python-kód nem tudja elérni a legtöbb könyvtárat azon a gépen, amelyen fut, a kivétel az aktuális könyvtár és az alkönyvtárak.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>A kifinomult fejlesztési és hibakeresési támogatás hiánya

A Python modul jelenleg nem támogatja az IDE funkciókat, például az intellisense-t és a hibakeresést. Továbbá, ha a modul futásidőben meghibásodik, a teljes Python-veremnyomat elérhető. De meg kell tekinteni a modul kimeneti naplójában. Jelenleg azt javasoljuk, hogy dolgozzon ki és debug Python parancsfájlok egy olyan környezetben, mint az IPython, majd importálja a kódot a modulba.

### <a name="single-data-frame-output"></a>Egyetlen adatkeret kimenete

A Python belépési pont csak akkor engedélyezett, hogy egyetlen adatkeret kimenetként. Jelenleg nem lehetséges tetszőleges Python-objektumok, például a betanított modellek közvetlenül vissza a Studio (klasszikus) futásidejű. Az [R-parancsfájl végrehajtása][execute-r-script]hasonlóan , amely nek ugyanaz a korlátozása, sok esetben lehetőség van objektumok at egy bájttömbbe, majd vissza, hogy egy adatkereten belül.

### <a name="inability-to-customize-python-installation"></a>A Python-telepítés testreszabásának képtelensége

Jelenleg az egyéni Python-modulok hozzáadásának egyetlen módja a korábban ismertetett zip fájlmechanizmus. Bár ez kis modulok esetében megvalósítható, nagy modulok (különösen natív DL-ekkel rendelkező modulok) vagy nagyszámú modul esetében nehézkes.

## <a name="next-steps"></a>További lépések

További információ: [Python fejlesztői központ](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
