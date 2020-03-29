---
title: Adatok átalakítása a felhőben a PowerShell használatával
description: Ez a PowerShell-parancsfájl átalakítja a felhőben lévő adatokat egy Azure HDInsight Spark-fürtön futó Spark-programmal.
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c09d0532b845472d0ccaac1ad57e3772630bb5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74932055"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell-parancsfájl – adatok átalakítása a felhőben az Azure Data Factory használatával

Ez a minta PowerShell-parancsfájl létrehoz egy folyamatot, amely átalakítja az adatokat a felhőben a Spark-program futtatásával egy Azure HDInsight Spark-fürtön. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Az Azure Storage-fiók.** Hozzon létre egy python-parancsfájlt és egy bemeneti fájlt, és töltse fel őket az Azure storage-ba. A Spark-program kimenetét ebben a tárfiókban tárolja a rendszer. Az igény szerinti Spark-fürt ugyanezt a tárfiókot használja elsődleges tárterületként.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Python-szkript feltöltése a Blob Storage-fiókba
1. Hozzon létre egy **WordCount_Spark.py** nevű Python-fájlt az alábbi tartalommal: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Cserélje le ** &lt;&gt; a storageAccountName-t** az Azure Storage-fiók nevére. Ezután mentse a fájlt. 
3. Az Azure Blob Storage-ban hozzon létre egy **adftutorial** nevű tárolót, ha még nem létezik. 
4. Hozzon létre egy **spark** mappát.
5. Hozzon létre egy **szkript** almappát a **spark** mappában. 
6. Töltse fel a **WordCount_Spark.py** fájlt a **szkript** almappába. 


### <a name="upload-the-input-file"></a>A bemeneti fájl feltöltése
1. Hozzon létre egy **minecraftstory.txt** nevű fájlt némi szöveges tartalommal. A Spark-program megszámolja a szavak számát ebben a szövegben. 
2. Hozzon létre `inputfiles` egy `spark` almappát a blobtároló mappájában. 
3. Töltse fel a `minecraftstory.txt` fájlt az `inputfiles` almappába. 

## <a name="sample-script"></a>Példaszkript
> [!IMPORTANT]
> Ez a parancsfájl JSON-fájlokat hoz létre, amelyek a c:\ Mappa.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A mintaparancsfájl futtatása után a következő paranccsal eltávolíthatja az erőforráscsoportot és a hozzá tartozó összes erőforrást:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Ha el szeretné távolítani az adat-előállítót az erőforráscsoportból, futtassa a következő parancsot: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzDataFactoryV2Linkedservice](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Csatolt szolgáltatást hoz létre az adat-előállítóban. A csatolt szolgáltatás adattort vagy számítást kapcsol össze egy adat-előállítóval. |
| [Set-AzDataFactoryV2pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Létrehoz egy folyamatot az adat-előállítóban. A folyamat egy vagy több olyan tevékenységet tartalmaz, amely egy bizonyos műveletet hajt végre. Ebben a folyamatban egy szikratevékenység átalakítja az adatokat egy Azure HDInsight Spark-fürtön futó program futtatásával. |
| [Invoke-AzDataFactoryV2pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Futást hoz létre a folyamathoz. Más szóval fut a folyamat. |
| [Get-AzDataFactoryV2Activityrun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | A folyamatban lévő tevékenység (tevékenységfuttatás) futtatásának részletei. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell-parancsfájlminták találhatók az [Azure Data Factory PowerShell-mintákban.](../samples-powershell.md)
