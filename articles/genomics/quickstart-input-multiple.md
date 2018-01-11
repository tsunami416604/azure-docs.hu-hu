---
title: "Rövid útmutató: Munkafolyamat elküldése több bemenet használatával | Microsoft Docs"
titleSuffix: Azure
description: "Ez a rövid útmutató feltételezi, hogy az msgen kliens telepítve van, és sikeresen futtatta a mintaadatokat a szolgáltatáson keresztül."
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: d410516f807b7914e15bed1fb93ee58d3e340d1e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Munkafolyamat elküldése azonos mintából származó több bemenet használatával

Ez a rövid útmutató ismerteti, hogyan lehet elküldeni egy munkafolyamatot a Microsoft Genomics szolgáltatásba, ha a bemenet több, **egyazon mintából származó** FASTQ- vagy BAM-fájlból áll. Ne feledje azonban, hogy FASTQ- és BAM-fájlokat **nem** küldhet egyszerre.

Ez a témakör feltételezi, hogy már telepítette és futtatta az `msgen` klienst, továbbá megismerkedett az Azure Storage használatával. Ha sikeresen elküldte a munkafolyamatot a rendelkezésre álló mintaadatokkal, készen áll a rövid útmutató folytatására. 


## <a name="multiple-bam-files"></a>Több BAM-fájl

### <a name="upload-your-input-files-to-azure-storage"></a>Bemeneti fájlok feltöltése az Azure Storage-ba
Tegyük fel, hogy a bemenet több BAM-fájlból áll (*reads.bam*, *additional_reads.bam*, és *yet_more_reads.bam*), amelyeket feltöltött saját *myaccount* nevű tárfiókjába az Azure-ban. Emellett rendelkezik az API URL-címével és a hozzáférési kulccsal. Azt szeretné, hogy a kimenetek a következő helyre kerüljenek: **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Feladat elküldése az `msgen`-ügyfélnek 

Egyszerre több BAM-fájlt is elküldhet, ha megadj a fájlok neveit az --input-blob-name-1 argumentumban. Ne feledje, hogy minden fájlnak egyazon mintából kell származnia, a sorrendjük azonban nem számít. Az alábbiakban parancssorból történő küldésre láthat példát a Windows és a Unix rendszerben, valamint egy konfigurációs fájl használatával. A sortörések az átláthatóság érdekében lettek hozzáadva:


Windows esetén:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
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
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Ha inkább konfigurációs fájlt szeretne használni, annak a következőket kell tartalmaznia:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Küldje el a `config.txt` fájlt a következő hívással: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Több párosított FASTQ-fájl

### <a name="upload-your-input-files-to-azure-storage"></a>Bemeneti fájlok feltöltése az Azure Storage-ba
Tegyük fel, hogy a bemenet több párosított FASTQ-fájlból áll: *reads_1.fq.gz* és *reads_2.fq.gz*,  *additional_reads_1.fq.gz* és *additional_reads_2.fq.gz*, valamint *yet_more_reads_1.fq.gz* és *yet_more_reads_2.fq.gz*. Az említett fájlokat már feltöltötte *myaccount* nevű Azure-beli tárfiókjába, valamint rendelkezik az API URL-címével és a hozzáférési kulccsal. Azt szeretné, hogy a kimenetek a következő helyre kerüljenek: **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Feladat elküldése az `msgen`-ügyfélnek 

A párosított FASTQ-fájloknak nem csupán egyazon mintából kell származniuk, de együtt is kell feldolgozni azokat.  A fájlok sorrendje számít, ha argumentumként kerülnek az --input-blob-name-1 és --input-blob-name-2 argumentumokba. 

Az alábbiakban parancssorból történő küldésre láthat példát a Windows és a Unix rendszerben, valamint egy konfigurációs fájl használatával. A sortörések az átláthatóság érdekében lettek hozzáadva:


Windows esetén:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
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
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
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
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Küldje el a `config.txt` fájlt a következő hívással: `msgen submit -f config.txt`

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben egyszerre több BAM-fájlt vagy párosított FASTQ-fájlt töltött fel az Azure Storage-ba, valamint elküldött egy munkafolyamatot a Microsoft Genomics szolgáltatásba az `msgen` Python-kliensen keresztül. A munkafolyamatok elküldésével, illetve a Microsoft Genomics szolgáltatásban használható egyéb parancsokkal kapcsolatos további információkért tekintse meg a [Gyakori kérdések](frequently-asked-questions-genomics.md) szakaszt. 