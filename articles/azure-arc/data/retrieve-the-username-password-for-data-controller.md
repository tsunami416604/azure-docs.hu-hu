---
title: A Felhasználónév és a Jelszó lekérése az ív-adatvezérlőhöz való kapcsolódáshoz
description: A Felhasználónév és a Jelszó lekérése az ív-adatvezérlőhöz való kapcsolódáshoz
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1c33cf21be0b4b1ea39a568d6df9fd90507dd454
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935950"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>A Felhasználónév és a Jelszó lekérése az ív-adatvezérlőhöz való kapcsolódáshoz

Előfordulhat, hogy az adatvezérlő felhasználónevét és jelszavát le kell kérnie. A futtatásakor a következő parancsokat kell megadnia. 

```console
azdata login
```

Ha Ön a fürt Kubernetes rendszergazdája. Ennek megfelelően a Kubernetes titkos kulcsokból lekért parancsok futtatásához szükséges jogosultságokat az Azure arc által megőrzött információk tárolják.

> [!NOTE]
>  Ha más nevet használt a névtérhez, amelyben az adatvezérlőt létrehozták, akkor az `-n arc` alábbi parancsokban található paramétert úgy kell módosítani, hogy annak a névtérnek a nevét használja, amelyhez az adatvezérlőt létrehozta.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Futtassa a következő parancsot a Felhasználónév lekéréséhez:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Futtassa a következő parancsot a jelszó lekéréséhez:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Futtassa a következő parancsot a Felhasználónév lekéréséhez:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Futtassa a következő parancsot a jelszó lekéréséhez:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Következő lépések

Más [forgatókönyvek](https://github.com/microsoft/Azure-data-services-on-Azure-Arc/tree/master/scenarios) kipróbálása
