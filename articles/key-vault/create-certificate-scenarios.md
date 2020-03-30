---
title: Tanúsítvány-létrehozás monitorozása és kezelése
description: A tanúsítványlétrehozási folyamat key vault-tal való létrehozására, figyelésére és az azokkal való interakcióra szolgáló lehetőségek széles skáláját bemutató forgatókönyvek.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b9ff80275cc89dde0db215856c2e134c4b273020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199733"
---
# <a name="monitor-and-manage-certificate-creation"></a>Tanúsítvány-létrehozás monitorozása és kezelése
A következőkre vonatkozik: Azure

A következő 

A forgatókönyvek / műveletek vázolt ebben a cikkben a következők:

- KV-tanúsítvány kérése támogatott kiállítóval
- Függőben lévő kérelem bekérése - a kérelem állapota "inProgress"
- Függőben lévő kérelem bekérése - a kérelem állapota "teljes"
- Függőben lévő kérelem bekérése - a függőben lévő kérelem állapota "törölve" vagy "sikertelen"
- Függőben lévő kérelem bekérése - a függőben lévő kérelem állapota "törölve" vagy "felülírva"
- Létrehozás (vagy importálás) függőben lévő kérelem esetén - az állapot "inProgress"
- Egyesítés, ha függőben lévő kérelem jön létre egy kibocsátóval (például DigiCert)
- Lemondás kérése, amíg a függőben lévő kérelem állapota "inProgress"
- Függőben lévő kérelemobjektum törlése
- KV-tanúsítvány manuális létrehozása
- Egyesítés függőben lévő kérelem létrehozásakor - manuális tanúsítvány létrehozása

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>KV-tanúsítvány kérése támogatott kiállítóval 

|Módszer|Kérés URI-ja|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

A következő példák ban egy "mydigicert" nevű objektumnak már elérhetőnek kell lennie a kulcstárolóban a kibocsátó szolgáltatóval, mint DigiCert. A tanúsítvány kiállítója egy entitás, amely az Azure Key Vaultban (KV) tanúsítványkiállítói erőforrásként képviselteti magát. A kv-tanúsítvány forrására vonatkozó információk szolgáltatására szolgál; kiállító neve, szolgáltatója, hitelesítő adatai és egyéb felügyeleti adatai.

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Függőben lévő kérelem bekérése - a kérelem állapota "inProgress"

|Módszer|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
Kap`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

VAGY

Kap`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Ha *request_id* van megadva a lekérdezésben, szűrőként működik. Ha a *lekérdezésben* és a függőben lévő objektumban a request_id eltérőek, a rendszer 404-es http-állapotkódot ad vissza.

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

## <a name="get-pending-request---request-status-is-complete"></a>Függőben lévő kérelem bekérése - a kérelem állapota "teljes"

### <a name="request"></a>Kérés

|Módszer|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Kap`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

VAGY

Kap`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Függőben lévő kérelem bekérése - a függőben lévő kérelem állapota "törölve" vagy "sikertelen"

### <a name="request"></a>Kérés

|Módszer|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Kap`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

VAGY

Kap`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> A *hibakód* értéke lehet "Tanúsítványkiállítói hiba" vagy "Elutasított kérés" a kibocsátó vagy a felhasználói hiba alapján.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Függőben lévő kérelem bekérése - a függőben lévő kérelem állapota "törölve" vagy "felülírva"
Egy függőben lévő objektum törölhető vagy felülírható egy létrehozási/importálási művelettel, ha állapota nem "inProgress".

|Módszer|Kérés URI-ja|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
Kap`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

VAGY

Kap`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Válasz

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Létrehozás (vagy importálás) függőben lévő kérelem esetén - az állapot "inProgress"
Egy függőben lévő objektumnak négy lehetséges állapota van; "inprogress", "canceled", "failed" vagy "completed".

Ha egy függőben lévő kérelem állapota "inprogress", létrehozása (és importálása) műveletek sikertelen lesz a http status code 409 (ütközés).

Ütközés kijavítása:

- Ha a tanúsítvány manuálisan jön létre, kiegészítheti a KV-tanúsítványt egyesítéssel vagy törléssel a függőben lévő objektumon.

- Ha a tanúsítványt egy kiállítóval hozzák létre, megvárhatja, amíg a tanúsítvány befejeződik, meghibásodik vagy megszakítja. Másik lehetőségként törölheti a függőben lévő objektumot.

> [!NOTE]
> Egy függőben lévő objektum törlése megszakíthatja vagy nem szakíthatja meg az x509-es tanúsítványkérelmet a szolgáltatónál.

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
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Egyesítés, ha függőben lévő kérelem jön létre egy kibocsátóval
Az egyesítés nem engedélyezett, ha egy függőben lévő objektumot egy kibocsátóval hoz létre, de akkor is engedélyezett, ha állapota "inProgress". 

Ha az x509-es tanúsítvány létrehozására irányuló kérelem valamilyen okból sikertelen vagy megszakítható, és ha egy x509-es tanúsítvány sávon kívüli eszközökkel visszakereshető, akkor egyesítési művelet végezhető a KV-tanúsítvány végrehajtásához.

|Módszer|Kérés URI-ja|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Kérés

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Válasz

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Lemondás kérése, amíg a függőben lévő kérelem állapota "inProgress"
Lemondás csak kérhető. A kérést lehet, hogy nem lehet törölni. Ha a kérelem nem "inProgress", a http állapota 400 (Bad Request) ad vissza.

|Módszer|Kérés URI-ja|
|------------|-----------------|
|Javítás|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
Javítás`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

VAGY

Javítás`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="delete-a-pending-request-object"></a>Függőben lévő kérelemobjektum törlése

> [!NOTE]
> A függőben lévő objektum törlése megszakíthatja vagy nem szakíthatja meg az x509-es tanúsítványkérelmet a szolgáltatónál.

|Módszer|Kérés URI-ja|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Kérés
Töröl`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

VAGY

Töröl`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="create-a-kv-certificate-manually"></a>KV-tanúsítvány manuális létrehozása
Manuális létrehozási folyamatsegítségével létrehozhat egy tetszőleges hitelesítésszolgáltatóval kiállított tanúsítványt. Állítsa a kibocsátó nevét "Ismeretlen" értékre, vagy ne adja meg a kiállító mezőt.

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Egyesítés függőben lévő kérelem létrehozásakor - manuális tanúsítvány létrehozása

|Módszer|Kérés URI-ja|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Kérés

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Elem neve|Kötelező|Típus|Verzió|Leírás|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Igen|tömb|\<bemutatkozik a> verzió|X509 tanúsítványlánc alap 64 karakterlánctömbként.|

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
            "validity_months": 12
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
