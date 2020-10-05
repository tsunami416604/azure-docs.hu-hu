---
title: Azure igazolási szabályzat készítése és aláírása
description: Az igazolási szabályzatok létrehozásával és aláírásával kapcsolatos magyarázat.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a3afb12ac831d87b03d0bb16d1b7ef553f1bb906
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90006819"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>Igazolási szabályzat létrehozása és aláírása

Az igazolási házirend egy Microsoft Azure igazolásra feltöltött fájl. Az Azure-igazolás rugalmasságot biztosít a szabályzatok egy tanúsítvány-specifikus házirend-formátumban való feltöltéséhez. Másik lehetőségként a szabályzat kódolt verziója is feltölthető a JSON webes aláírásban. A szabályzat rendszergazdája felelős az igazolási szabályzat írásához. A legtöbb igazolási forgatókönyvben a függő entitás házirend-rendszergazdaként működik. Az igazolási hívást végző ügyfél tanúsító igazolást küld, amelyet a szolgáltatás elemez és konvertál a bejövő jogcímekre (tulajdonságok, érték). A szolgáltatás ezután feldolgozza a jogcímeket a Szabályzatban meghatározottak alapján, és visszaadja a számított eredményt.

A házirend olyan szabályokat tartalmaz, amelyek meghatározzák az igazolási token engedélyezési feltételeit, tulajdonságait és tartalmát. Egy példaként szolgáló házirend-fájl az alábbihoz hasonlóan néz ki:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
A házirend-fájl három szegmensből áll, ahogy az a fentiekben látható:

- **verzió**: a verziószám a követett nyelvtan verziószáma. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Jelenleg az egyetlen támogatott verzió a 1,0-es verzió.

- **engedélyezési szabályok**: az első ellenőrizendő jogcím-szabályok gyűjteménye annak megállapítására, hogy az Azure-igazolásnak folytatódnia kell-e a **issuancerules**. A jogcím szabályai a definiált sorrendben lesznek érvényesek.

- **issuancerules**: olyan jogcím-szabályok gyűjteménye, amelyeket a rendszer kiértékel, hogy további információkat adjon hozzá az igazolási eredményhez a Szabályzatban meghatározottak szerint. A jogcím szabályai a definiált sorrendben érvényesek, és szintén nem kötelezőek.

További információért lásd a [jogcím és a jogcím szabályait](claim-rule-grammar.md) .
   
## <a name="drafting-the-policy-file"></a>A házirend-fájl előkészítése

1. Hozzon létre egy új fájlt.
1. Adjon hozzá verziót a fájlhoz.
1. Vegyen fel szakaszt a **engedélyezési szabályok** és a **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Az engedélyezési szabályok feltételek nélkül tartalmazzák a Megtagadás () műveletet, így biztosítva a kiállítási szabályok feldolgozását. Azt is megteheti, hogy az engedélyezési szabály is tartalmazhat engedélyezési () műveletet a kiállítási szabályok feldolgozásának engedélyezéséhez.
  
4. Jogcím-szabályok hozzáadása az engedélyezési szabályokhoz

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Ha a bejövő jogcímek készlete olyan jogcímet tartalmaz, amely megfelel a típusnak, az értéknek és a kiállítónak, az engedély () művelet tájékoztatja a házirend-motort a **issuancerules**feldolgozásáról.
  
5. Adja hozzá a **issuancerules**a jogcím-szabályokat.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  A kimenő jogcímek készlete a következőket fogja tartalmazni:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Az összetett szabályzatok hasonló módon állíthatók elő. További információ: [igazolási házirendek példái](policy-examples.md).
  
6. Mentse a fájlt.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>A házirend-fájl létrehozása JSON webes aláírás formátumban

Miután létrehozott egy házirendet a szabályzat JWS formátumban való feltöltéséhez, kövesse az alábbi lépéseket.

1. A JWS, az RFC 7515 és a szabályzat (UTF-8 kódolású) előállítása adattartalomként
     - A Base64Url-kódolású házirend hasznos azonosítójának a következőnek kell lennie: "AttestationPolicy".
     
     Minta JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. Választható Írja alá a szabályzatot. Az Azure-igazolás a következő algoritmusokat támogatja:
     - **Nincs**: ne írja alá a szabályzat hasznos adatait.
     - **RS256**: a szabályzat hasznos adatainak aláírására szolgáló támogatott algoritmus

3. Töltse fel a JWS, és ellenőrizze a szabályzatot.
     - Ha a házirend-fájl szintaktikai hibáktól mentes, a szolgáltatás elfogadja a házirend-fájlt.
     - Ha a házirend-fájl szintaktikai hibákat tartalmaz, a szolgáltatás visszautasítja a házirend-fájlt.

## <a name="signing-the-policy"></a>A szabályzat aláírása

Az alábbiakban egy példa Python-szkriptet talál a házirend-aláírási művelet végrehajtásához

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>További lépések
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)
- [SGX ENKLÁVÉHOZ enklávé igazolása kód-minták használatával](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
