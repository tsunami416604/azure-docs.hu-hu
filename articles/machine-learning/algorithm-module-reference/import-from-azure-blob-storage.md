---
title: 'Importálás az Azure Blob Storageból: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ebből a témakörből megtudhatja, hogyan használhatja az Azure Blob Storage-ból való importálást az Azure Blob Storage Azure Machine Learning modulból, hogy az adatok a Machine learning-kísérletekben is használhatók legyenek.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fea64070c496379351bb75f2a38aba9b4db70dcd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128719"
---
# <a name="import-from-azure-blob-storage-module"></a>Importálás az Azure Blob Storage modulból

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal olvashatók be az adatok az Azure Blob Storage-ból, így a Machine learning-kísérletekben tárolt adatok is használhatók.  

Az Azure Blob Service nagy mennyiségű, például bináris adatmennyiségű adattárolásra szolgál. Az Azure-Blobok bárhonnan, HTTP-vagy HTTPS-kapcsolaton keresztül is elérhetők. A blob Storage típusától függően szükség lehet a hitelesítésre. 

- A nyilvános blobokat bárki vagy SAS URL-címmel rendelkező felhasználó is elérheti.
- A privát blobokhoz bejelentkezési és hitelesítő adatok szükségesek.

A blob Storage-ból történő importálás megköveteli, hogy az adatok a **Blobok blokkolása** formátumot használó blobokban legyenek tárolva. A blobban tárolt fájloknak vesszővel tagolt (CSV) vagy tabulátorral tagolt (TSV) formátumot kell használniuk. Ha elolvassa a fájlt, a rekordok és a megfelelő attribútumok fejlécei sorokként töltődnek be a memóriába adatkészletként.


Javasoljuk, hogy az importálás előtt profilokat készítsen az adataihoz, hogy a séma a várt módon legyen. Az importálási folyamat megvizsgál néhány fő sort a séma meghatározásához, de a későbbi sorok további oszlopokat is tartalmazhatnak, vagy hibákat okozhatnak.



## <a name="manually-set-properties-in-the-import-data-module"></a>Tulajdonságok manuális beállítása az Adatimportálási modulban

A következő lépések azt írják le, hogyan konfigurálhatja manuálisan az importálási forrást.

1. Adja hozzá az Adatimportálási modult a kísérlethez. Ez a modul a felületen, az **adatok bemenetében és kimenetében** található.

2. Az **adatforrás**területen válassza az **Azure Blob Storage**lehetőséget.

3. A **Hitelesítés típusa**beállításnál válassza a **nyilvános (SAS URL)** lehetőséget, ha biztos benne, hogy az adatok nyilvános adatforrásként vannak megadva. Az SAS URL-cím egy olyan időkorlátos URL-cím, amely egy Azure Storage-segédprogrammal létrehozható nyilvános hozzáféréshez használható.

    Egyéb esetben válassza a **fiók**lehetőséget.

4. Ha az adatok olyan **nyilvános** blobban találhatók, amely sas URL-cím használatával érhető el, nincs szükség további hitelesítő adatokra, mert az URL-karakterlánc tartalmazza a letöltéshez és a hitelesítéshez szükséges összes információt.

    Az **URI** mezőben írja be vagy illessze be a fiókot és a nyilvános blobot meghatározó teljes URI-t.



5. Ha az adatai **privát** fiókkal rendelkeznek, meg kell adnia a hitelesítő adatokat, beleértve a fióknevet és a kulcsot is.

    - A **fiók neve**mezőbe írja be vagy illessze be annak a fióknak a nevét, amely az elérni kívánt blobot tartalmazza.

        Ha például a Storage-fiók `http://myshared.blob.core.windows.net`teljes URL-címe, akkor gépelje be `myshared`a következőt:.

    - A **fiók kulcsa**mezőben illessze be a fiókhoz társított tárterület-hozzáférési kulcsot.

        Ha nem ismeri a hozzáférési kulcsot, tekintse meg a jelen cikk "az Azure Storage-fiókok kezelése" című szakaszát. [Tudnivalók az Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account)-fiókokról.

6. A **tároló, könyvtár vagy blob elérési útja**mezőbe írja be a lekérdezni kívánt blob nevét.

    Ha például egy **data01. csv** nevű fájlt töltött fel egy **mymldata**nevű fiók `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` **trainingdata** , a fájl teljes URL-címe a következő lesz:.

    Ezért a **tároló, könyvtár vagy blob mező elérési útján**a következőt kell beírnia:`trainingdata/data01.csv`

    Több fájl importálásához használhatja a helyettesítő karaktereket `*` (csillag) vagy `?` (kérdőjel).

    Tegyük fel például, hogy `trainingdata` a tároló több `data`, kompatibilis formátumú fájlt tartalmaz, az alábbi specifikáció segítségével olvashatja el az összes fájlt, és összefűzheti őket egyetlen adatkészletbe:

    `trainingdata/data*.csv`

    A tárolók neveiben nem használhatók helyettesítő karakterek. Ha több tárolóból kell fájlokat importálnia, használja az **adatok importálása** modul külön példányát az egyes tárolók számára, majd egyesítse az adatkészleteket a [sorok hozzáadása](./add-rows.md) modul használatával.

    > [!NOTE]
    > Ha a lehetőséget választotta, a **gyorsítótárazott eredmények használatával**a tárolóban lévő fájlokon végrehajtott módosítások nem indítják el a kísérletben lévő adatfrissítést.

7. **Blob fájlformátum**esetén olyan beállítást válasszon, amely megadja a blobban tárolt adatformátumot, így a Azure Machine learning megfelelően feldolgozhatják az adatfeldolgozást. A következő formátumok támogatottak:

    - **CSV**: A vesszővel tagolt értékek (CSV) a fájlok exportálásának és importálásának alapértelmezett tárolási formátuma Azure Machine Learningban. Ha az adathalmaz már tartalmaz egy fejlécet, ügyeljen arra, hogy a **fájl fejléce**legyen, vagy a fejléc adatsorként lesz kezelve.

       

    - **TSV**: A tabulátorral tagolt értékek (TSV) számos gépi tanulási eszköz által használt formátum. Ha az adathalmaz már tartalmaz egy fejlécet, ügyeljen arra, hogy a **fájl fejléce**legyen, vagy a fejléc adatsorként lesz kezelve.

       

    - **ARFF**: Ez a formátum támogatja a fájlok importálását a WEKA eszközkészlet által használt formátumban. 

   

8. Futtassa a kísérletet.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 