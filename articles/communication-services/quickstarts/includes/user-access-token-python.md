---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947217"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 vagy újabb.
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../create-communication-resource.md).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. Nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Szövegszerkesztő használatával hozzon létre egy **Issue-tokens.py** nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a program struktúráját, beleértve az alapszintű kivételek kezelését. Ehhez a rövid útmutatóhoz tartozó forráskódot a következő részekben adja hozzá ehhez a fájlhoz.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>A csomag telepítése

Miközben még mindig az alkalmazás könyvtárában van, telepítse a Python-csomaghoz készült Azure Communication Services Felügyeleti ügyféloldali kódtárat a `pip install` paranccsal.

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A-példány létrehozása a- `CommunicationIdentityClient` val a kapcsolatok karakterláncával. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../create-communication-resource.md#store-your-connection-string).

Adja hozzá ezt a kódot a `try` blokkon belül:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>Felhasználó létrehozása

Az Azure kommunikációs szolgáltatások egy egyszerűsített identitási könyvtárat tartanak fenn. A `create_user` metódus használatával hozzon létre egy új bejegyzést a címtárban egyedi értékkel `Id` . Az alkalmazás felhasználóinak és a kommunikációs szolgáltatások által generált identitások (például az alkalmazás-kiszolgáló adatbázisában való tárolás) közötti leképezést kell fenntartani.

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Felhasználói hozzáférési tokenek kiadása

A `issue_token` metódus használatával kiállíthatja a kommunikációs szolgáltatások felhasználójának hozzáférési jogkivonatát. Ha nem adja meg a választható `user` paramétert, a rendszer új felhasználót hoz létre, és visszaadja a tokent.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

A felhasználói hozzáférési tokenek olyan rövid élettartamú hitelesítő adatok, amelyeket újra kell adni ahhoz, hogy a felhasználók megakadályozzák a szolgáltatás megszakadását. A `expires_on` Response tulajdonság a jogkivonat élettartamát jelzi.

## <a name="revoke-user-access-tokens"></a>Felhasználói hozzáférési tokenek visszavonása

Bizonyos esetekben előfordulhat, hogy explicit módon vissza kell vonnia a felhasználói hozzáférési jogkivonatokat, például amikor egy felhasználó módosítja a szolgáltatásban való hitelesítéshez használt jelszót. Ez a funkció az Azure kommunikációs szolgáltatások felügyeleti ügyféloldali könyvtárán keresztül érhető el.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Felhasználó törlése

Az identitások törlése visszavonja az összes aktív jogkivonatot, és megakadályozza, hogy az identitások számára további jogkivonatokat bocsásson ki. Emellett eltávolítja a felhasználóhoz társított összes megőrzött tartalmat is.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>A kód futtatása

A konzol parancssorában navigáljon a *Issue-token.py* fájlt tartalmazó könyvtárra, majd futtassa az alábbi `python` parancsot az alkalmazás futtatásához.

```console
python ./issue-token.py
```
