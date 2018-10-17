---
title: 'Rövid útmutató: Munkafolyamat elküldése FASTQ bemeneti fájlokkal | Microsoft Genomics'
titleSuffix: Azure
description: Ez a rövid útmutató feltételezi, hogy az msgen kliens telepítve van, és sikeresen futtatta a mintaadatokat a szolgáltatáson keresztül.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: acbcceb32ec54ab85db05ef743e9c10cd8cf025c
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735849"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Munkafolyamat elküldése FASTQ bemeneti fájlokkal a Microsoft Genomics szolgáltatásba

Ez a rövid útmutató ismerteti, hogyan lehet elküldeni egy munkafolyamatot a Microsoft Genomics szolgáltatásba, ha a bemenet egy FASTQ-fájlpárból áll. Ez a témakör feltételezi, hogy már telepítette és futtatta az `msgen` klienst, továbbá megismerkedett az Azure Storage használatával. Ha sikeresen elküldte a munkafolyamatot a rendelkezésre álló mintaadatokkal, készen áll a rövid útmutató folytatására. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Előkészületek: FASTQ-fájlok feltöltése az Azure Storage-ba
Tegyük fel, hogy két fájllal rendelkezik (*reads_1.fq.gz* és *reads_2.fq.gz*), amelyeket feltöltött saját *myaccount* nevű tárfiókjába az Azure-ban: **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** és **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>**. Emellett rendelkezik az API URL-címével és a hozzáférési kulccsal. Azt szeretné, hogy a kimenetek a következő helyre kerüljenek: **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


## <a name="submit-your-job-to-the-msgen-client"></a>Feladat elküldése az `msgen`-ügyfélnek 

Itt található az a minimális argumentumkészlet, amelyet meg kell adnia az `msgen` klienshez (a sortörések az átláthatóság érdekében lettek hozzáadva):

Windows esetén:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

Unix esetén:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Ha inkább konfigurációs fájlt szeretne használni, annak a következőket kell tartalmaznia:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Küldje el a `config.txt` fájlt a következő hívással: `msgen submit -f config.txt`

## <a name="next-steps"></a>További lépések
Ebben a cikkben egy FASTQ-fájlpárt töltött fel az Azure Storage-ba, valamint elküldött egy munkafolyamatot a Microsoft Genomics szolgáltatásba az `msgen` Python-kliensen keresztül. A munkafolyamatok elküldésével, illetve a Microsoft Genomics szolgáltatásban használható egyéb parancsokkal kapcsolatos további információkért tekintse meg a [Gyakori kérdések](frequently-asked-questions-genomics.md) szakaszt. 
