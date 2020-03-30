---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179337"
---
Ha rendelkezik egy megosztott hozzáférésű aláírás (SAS) URL-címet, amely hozzáférést biztosít a tárfiók ban lévő erőforrásokhoz, használhatja a SAS-t egy kapcsolati karakterláncban. Mivel a SAS tartalmazza a kérelem hitelesítéséhez szükséges információkat, a SAS-szal létesített kapcsolati karakterlánc biztosítja a protokollt, a szolgáltatásvégpontot és az erőforrás eléréséhez szükséges hitelesítő adatokat.

Ha megosztott hozzáférési aláírást tartalmazó kapcsolati karakterláncot szeretne létrehozni, adja meg a karakterláncot a következő formátumban:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Minden szolgáltatásvégpont nem kötelező, bár a kapcsolati karakterláncnak legalább egy feltételt kell tartalmaznia.

> [!NOTE]
> Https használata a SAS ajánlott eljárásként ajánlott.
>
> Ha egy konfigurációs fájlban egy kapcsolati karakterláncban sas-t ad meg, előfordulhat, hogy speciális karaktereket kell kódolnia az URL-címben.
>
>

### <a name="service-sas-example"></a>Szolgáltatás SAS példa
Íme egy példa egy kapcsolati karakterláncra, amely tartalmazza a blobstorage szolgáltatás SAS-át:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

És itt egy példa az azonos kapcsolat string kódolása speciális karakterek:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Példa a fiók SAS-ra
Íme egy példa egy kapcsolati karakterláncra, amely tartalmazza a Blob és a File storage fiókSAS-t. Vegye figyelembe, hogy mindkét szolgáltatás végpontjai meg vannak adva:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

És íme egy példa ugyanarra a kapcsolati karakterláncra URL-kódolással:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

