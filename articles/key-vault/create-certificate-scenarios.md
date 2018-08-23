---
title: Tanúsítvány-létrehozás monitorozása és kezelése
description: Forgatókönyvek létrehozására vonatkozó lehetőségek bemutatásához, figyelés és a tanúsítvány létrehozása implementálására feldolgozni a Key Vault.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 0d0995aa-b60d-4811-be12-ba0a45390197
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: 80f350b9b83438ee04540527cce0ea6821d148ca
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42057236"
---
# <a name="monitor-and-manage-certificate-creation"></a>Tanúsítvány-létrehozás monitorozása és kezelése
A következőkre vonatkozik: Azure  

A következő 

A forgatókönyvek / ebben a cikkben leírt műveletek a következők:

- A támogatott kiállító KV tanúsítvány kérése
- Függőben lévő kérelem beolvasása – a kérelem állapotának "inprogress" lehet.
- Függőben lévő kérelem beolvasása – a kérelem állapotának "kész"
- Függőben lévő kérelem - "megszakítva" vagy "sikertelen" állapotú függőben lévő kérelem beolvasása
- Függőben lévő kérelem - állapot "törölt" vagy "felülírja a" függőben lévő kérelem beolvasása
- Hozzon létre (vagy importálása) mikor függőben lévő kérelem létezik - állapot "inprogress lehet":
- Függő kérelem egyesítési jön létre a egy kiállító (például DigiCert)
- Amíg a függőben lévő kérelem állapota "inProgress" visszavonást
- Függőben lévő kérelem-objektum törlése
- Hozzon létre manuálisan egy KV tanúsítványt
- Egyesítse a függőben lévő kérelem létrehozásakor - tanúsítványok manuális létrehozása

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>A támogatott kiállító KV tanúsítvány kérése 

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

Az alábbi példák már elérhetők a key vaulttal és a DigiCert kibocsátó szolgáltató "mydigicert" nevű objektum szükséges. A tanúsítvány kiállítója jelöli az Azure Key Vault (KV) CertificateIssuer erőforrásként entitás. A forrás-KV tanúsítvány; kapcsolatos információk megadására szolgál kiállító neve, szolgáltató, hitelesítő adatok és egyéb felügyeleti adatait.  

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Függőben lévő kérelem beolvasása – a kérelem állapotának "inprogress" lehet.

|Módszer|Kérés URI-ja|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Kérés  
 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 VAGY  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

> [!NOTE]
>  Ha *request_id* van megadva a lekérdezésben, azt úgy viselkedik, mint egy szűrőt. Ha a *request_id* a lekérdezésben, és a függőben lévő objektumban eltérőek, egy http-állapotkód: 404 adja vissza.  

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

## <a name="get-pending-request---request-status-is-complete"></a>Függőben lévő kérelem beolvasása – a kérelem állapotának "kész"

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Függőben lévő kérelem - "megszakítva" vagy "sikertelen" állapotú függőben lévő kérelem beolvasása  

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
> Értékét a *errorcode* kibocsátó vagy felhasználói hiba rendre alapján "Kérelem elutasítva" vagy "Hiba a tanúsítvány kiállítója" lehet.  

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Függőben lévő kérelem - állapot "törölt" vagy "felülírja a" függőben lévő kérelem beolvasása  
 A függőben lévő objektum törölhető vagy egy létrehozás/importálás művelet felülírja, ha annak állapota nem "inprogress" lehet.

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

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Hozzon létre (vagy importálása) mikor függőben lévő kérelem létezik - állapot "inprogress lehet":
 Egy függőben lévő objektumnak négy lehetséges állapotok; "inprogress", "megszakítva", "sikertelen" vagy "kész".

 Ha a függőben lévő kérelem állapota "inprogress" lehet, hozzon létre (és importálása) művelet sikertelen lesz, és a egy http-állapotkód: 409 (Ütközés).  

 Ütközés elhárításához:  

 - Ha a tanúsítvány manuális létrehozása folyamatban van, végezze el a KV tanúsítványt az egyesítési végrehajtásával, vagy törölje a függőben lévő objektum.  

 - Ha a tanúsítvány a egy kiállító létrehozása van folyamatban, megvárhatja mindaddig, amíg a tanúsítvány befejeződik, sikertelen vagy megszakítva. Másik lehetőségként törölheti a függőben lévő objektum.

> [!NOTE]
> Előfordulhat, hogy egy függőben lévő objektum törlése, vagy nem lehetséges, hogy megszakítja a x509 tanúsítványkérelem-szolgáltatóval.  

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

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Függőben lévő kérelem létrehozásakor a egy kiállító egyesítése
 Egyesítési nem engedélyezett, ha egy függőben lévő objektumot hoz létre a egy kiállító, de van engedélyezve, ha az állapot "inprogress" lehet. 

 Ha a x509 létrehozására vonatkozó kérelem tanúsítvány meghibásodik, vagy valamilyen okból megszakítja, és a egy x509 tanúsítvány a sávon kívüli módon kérhető, ha egy merge művelet teheti meg a KV tanúsítvány befejezéséhez.  

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

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Amíg a függőben lévő kérelem állapota "inProgress" visszavonást  
 Lemondás csak igényelni lehet.  Egy kérelem vagy előfordulhat, hogy nem szakítható meg. Ha a kérés nem "inprogress" lehet, 400 (hibás kérés) http-állapotkódot adja vissza.  

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
> Előfordulhat, hogy a függőben lévő objektum törlése, vagy nem lehetséges, hogy megszakítja a x509 tanúsítványkérelem-szolgáltatóval.  

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
 A tanúsítvány kibocsátása keresztül manuális létrehozását a kiválasztott hitelesítésszolgáltató hozhat létre. Beállítása a kibocsátó neve "Ismeretlen", vagy adja meg a kibocsátó mező.  

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Egyesítse a függőben lévő kérelem létrehozásakor - tanúsítványok manuális létrehozása  

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
|x5c|Igen|tömb|\<Bemutatkozik a verzió >|X509 tanúsítványlánc, base 64 karakterlánc-tömbben.|  

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
