---
title: Megfigyelés és kezelés tanúsítvány létrehozása
description: Forgatókönyvek számos olyan lehetőséget létrehozásához, amely tartalmazza, figyelés, és a tanúsítvány létrehozása való interakció feldolgozni az Key Vault.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 0d0995aa-b60d-4811-be12-ba0a45390197
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: e1ea77304fa59b67e0e28a4c7e0b13633eeeff6f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="monitor-and-manage-certificate-creation"></a>Megfigyelés és kezelés tanúsítvány létrehozása
A következőkre vonatkozik: Azure  

A következő 

A forgatókönyvek / ebben a cikkben leírt műveletek esetében:

- A támogatott kiállító KV tanúsítvány igénylése
- Függőben lévő kérelem beszerzése – a szolgáltatáskérés állapota "inprogress" lehet.
- Függőben lévő kérelmet Get - a szolgáltatáskérés állapota "kész"
- Függőben lévő kérelem – függőben lévő kérelem "megszakítva" vagy "sikertelen" állapotú beolvasása
- Függőben lévő kérelem – függőben lévő kérelem "törölt" vagy "felül" állapotú beolvasása
- Hozzon létre (vagy importáljon) mikor függőben lévő kérelem létezik - állapot "inprogress lehet":
- Partícióegyesítési kérelem függőben jön létre a egy kiállító (például DigiCert)
- A megszakítási kérelem közben a függőben lévő kérelem állapota "inprogress lehet"
- Függőben lévő kérelem-objektum törlése
- Hozzon létre manuálisan egy KV tanúsítványt
- Amikor létrejön egy függőben lévő kérelem - tanúsítványok manuális létrehozása egyesítése

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>A támogatott kiállító KV tanúsítvány igénylése 

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

Az alábbi példák már használhatók a kibocsátó szolgáltató DigiCert a key vaultban lévő "mydigicert" nevű objektum szükséges. Kiállítók munkavégzés további információkért lásd: [kibocsátói tanúsítvány](/rest/api/keyvault/certificate-issuers.md).  

### <a name="request"></a>Kérés  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    },  
  "issuer": {  
       "name": "mydigicert",  
    "cty": "OV-SSL",  
    }  
  }  
}  

```  

### <a name="response"></a>Válasz  

```  
StatusCode: 202, ReasonPhrase: 'Accepted'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "mydigicert"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "InProgress",  
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="get-pending-request---request-status-is-inprogress"></a>Függőben lévő kérelem beszerzése – a szolgáltatáskérés állapota "inprogress" lehet.

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Kérés  
 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 VAGY  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

> [!NOTE]
>  Ha *request_id* van megadva a lekérdezésben szűrő hasonlóan működik. Ha a *request_id* a lekérdezés és a függőben lévő objektum nem egyezik, a 404-es http-állapotkódot adja vissza.  

### <a name="response"></a>Válasz  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "inProgress",  
  "status_details": "…",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="get-pending-request---request-status-is-complete"></a>Függőben lévő kérelmet Get - a szolgáltatáskérés állapota "kész"

### <a name="request"></a>Kérés  

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 VAGY  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Válasz  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "completed",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
   "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"  
}  

```  

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Függőben lévő kérelem – függőben lévő kérelem "megszakítva" vagy "sikertelen" állapotú beolvasása  

### <a name="request"></a>Kérés  

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 VAGY  

 GET  `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Válasz  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "failed",  
  "status_details": "",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
   "error":  
    {  
        "code": "<errorcode>",    
        "message": "<message>"  
    }  
}  

```  

> [!NOTE]
> Értékét a *errorcode* kibocsátó vagy a felhasználó hiba rendre alapján "Vonatkozó kérés elutasítva" vagy "Tanúsítvány kiállítója error" lehet.  

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Függőben lévő kérelem – függőben lévő kérelem "törölt" vagy "felül" állapotú beolvasása  
 A függőben lévő objektumok törölhető vagy a create/importálási művelete felül, ha az állapota nem "inprogress" lehet.

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Kérés  
 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 VAGY  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Válasz  

```  
StatusCode: 404, ReasonPhrase: 'Not Found'  
{  
  "error":  
    {  
         "code": "PendingCertificateNotFound",  
        "message": "…"  
    }  
}  

```  

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Hozzon létre (vagy importáljon) mikor függőben lévő kérelem létezik - állapot "inprogress lehet":
 A függőben lévő objektumnak négy lehetséges állapota; "inprogress" lehet, "megszakítva", "sikertelen" vagy "kész".

 Ha egy függőben lévő kérelem állapota "inprogress" lehet, hozzon létre (és importálása) egy http-állapotkód: 409 (Ütközés) a műveletek sikertelenek lesznek.  

 Ütközés elhárításához:  

 - Ha a tanúsítvány manuális létrehozása folyamatban van, végezze el a KV tanúsítvány egyesítésével végrehajtásával, vagy törölje a függőben lévő objektum.  

 - Ha a tanúsítvány a egy kiállító kerül létrehozásra, megvárhatja, amíg nem fejeződik, sikertelen vagy megszakadt a tanúsítványt. Azt is megteheti törölheti a függőben lévő objektum.

> [!NOTE]
> Előfordulhat, hogy egy függőben lévő objektum törlése, vagy nem lehetséges, hogy megszakítja a x509 tanúsítványkérelmet a szolgáltatónál.  

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

### <a name="request"></a>Kérés  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    },  
  "issuer": {  
       "name": "mydigicert"  
    }  
  }  
}  

```  

### <a name="response"></a>Válasz  

```  
StatusCode: 409, ReasonPhrase: 'Conflict'  
{  
  "error":  
    {  
        "code": "Forbidden",  
        "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."  
    }  
}  

```  

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Ha függő kérelem jön létre a kibocsátó egyesítése
 Egyesítés nem engedélyezett, ha egy függőben lévő objektum kibocsátó jön létre, de van engedélyezve, ha állapotában "inprogress" lehet. 

 Ha a x509 létrehozására vonatkozó kérelem tanúsítvány meghibásodik, vagy valamilyen okból megszakítja, és egy tanúsítványt a sávon kívüli azt jelenti, hogy lekérhetők x509, ha egy partícióegyesítési művelet végezhető a KV tanúsítvány befejezéséhez.  

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>Kérés  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

### <a name="response"></a>Válasz  

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'  
{  
  "error":  
    {  
       "code": "Forbidden",  
       "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."  
    }  
}  

```  

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>A megszakítási kérelem közben a függőben lévő kérelem állapota "inprogress lehet"  
 A megszakítási csak kérhető.  A kérelem vagy nem lehet megszakítani. Ha a kérés nem "inprogress" lehet, egy http-állapot 400 (hibás kérés) adja vissza.  

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Kérés  
 JAVÍTÁS `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 VAGY  

 JAVÍTÁS `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

```json
{  
  "cancellation_requested": true  
}  

```  

### <a name="response"></a>Válasz  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": true,  
  "status": "inProgress",  
  "status_details": "…",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="delete-a-pending-request-object"></a>Függőben lévő kérelem-objektum törlése  

> [!NOTE]
> Előfordulhat, hogy a függőben lévő objektum törlése, vagy nem lehetséges, hogy megszakítja a x509 tanúsítványkérelmet a szolgáltatónál.  

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Kérés  
 TÖRLÉSE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 VAGY  

 TÖRLÉSE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Válasz  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "inProgress",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
}  
```  

## <a name="create-a-kv-certificate-manually"></a>Hozzon létre manuálisan egy KV tanúsítványt  
 Létrehozhat egy hitelesítésszolgáltató az Ön által választott manuális létrehozását folyamaton keresztül kiadott tanúsítványt. A kibocsátó neve "Ismeretlen" beállítva, vagy ne adja meg a kiállító mezőjében.  

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

### <a name="request"></a>Kérés  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    }  
  "issuer": {  
       "name": "Unknown"  
    }  
  }  
}  

```  

### <a name="response"></a>Válasz  

```  
StatusCode: 202, ReasonPhrase: 'Accepted'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "Unknown"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "status": "inProgress",  
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Amikor létrejön egy függőben lévő kérelem - tanúsítványok manuális létrehozása egyesítése  

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>Kérés  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

|Elem neve|Szükséges|Típus|Verzió|Leírás|  
|------------------|--------------|----------|-------------|-----------------|  
|x5c|Igen|tömb|\<Introducing verzió >|X509 tanúsítványlánc, base 64 karakterlánc-tömbben.|  

### <a name="response"></a>Válasz  

```  
StatusCode: 201, ReasonPhrase: 'Created'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"  
{  
"id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "cer": "……de34534……",  
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",  
    "attributes": {  
        "enabled": true,  
        "exp": 1530394215,  
        "nbf": 1435699215,  
        "created": 1435699919,  
        "updated": 1435699919  
    },  
    "pending": {  
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"  
    },  
    "policy": {  
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",  
        "key_props": {  
            "exportable": false,  
            "kty": "RSA",  
            "key_size": 2048,  
            "reuse_key": false  
        },  
        "secret_props": {  
            "contentType": "application/x-pkcs12"  
        },  
        "x509_props": {  
            "subject": "CN=Mycert1",  
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],  
                       "validity_months":12  
        },  
        "lifetime_actions": [{  
            "trigger": {  
                "lifetime_percentage": 80  
            },  
            "action": {  
                "action_type": "EmailContacts"  
            }  
        }],  
        "issuer": {  
            "name": "Unknown"  
        },  
        "attributes": {  
            "enabled": true,  
            "created": 1435699811,  
            "updated": 1435699811  
        }  
    }  
}  

```

## <a name="see-also"></a>Lásd még:
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
